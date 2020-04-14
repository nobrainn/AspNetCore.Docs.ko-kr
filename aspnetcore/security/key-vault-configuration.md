---
title: ASP.NET 코어의 Azure 키 볼트 구성 공급자
author: rick-anderson
description: Azure 키 볼트 구성 공급자를 사용하여 런타임에 로드된 이름-값 쌍을 사용하여 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228168"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET 코어의 Azure 키 볼트 구성 공급자

[앤드류 스탠튼-간호사](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 Microsoft [Azure 키 볼트](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용하여 Azure 키 볼트 암호에서 앱 구성 값을 로드하는 방법을 설명합니다. Azure Key Vault는 앱 및 서비스에서 사용하는 암호화 키 및 비밀을 보호하는 데 도움을 주는 클라우드 기반 서비스입니다. ASP.NET 핵심 앱과 함께 Azure 키 볼트를 사용하기 위한 일반적인 시나리오는 다음과 같습니다.

* 중요한 구성 데이터에 대한 액세스를 제어합니다.
* 구성 데이터를 저장할 때 FIPS 140-2 수준 2 검증된 하드웨어 보안 모듈(HSM)에 대한 요구 사항을 충족합니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [방법)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>패키지

[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 패키지 참조를 추가합니다.

## <a name="sample-app"></a>샘플 앱

샘플 앱은 *Program.cs* 파일 맨 위에 `#define` 있는 문으로 결정된 두 가지 모드 중 하나에서 실행됩니다.

* `Certificate`&ndash; Azure 키 볼트 클라이언트 ID 및 X.509 인증서를 사용하여 Azure 키 자격 증명 모음에 저장된 암호에 액세스하는 방법을 보여 줍니다. 이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 호스트에 배포된 모든 위치에서 실행할 수 있습니다.
* `Managed`&ndash; [Azure 리소스에 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 앱의 코드 또는 구성에 저장된 자격 증명 없이 Azure AD 인증을 사용하여 Azure Key Vault에 앱을 인증하는 방법을 보여 줍니다. 관리되는 ID를 사용하여 인증하는 경우 Azure AD 응용 프로그램 ID 및 암호(클라이언트 보안)가 필요하지 않습니다. 샘플 `Managed` 의 버전을 Azure에 배포해야 합니다. [Azure 리소스에 대한 관리되는 ID 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.

프로세서 전 지시문 ()`#define`을 사용하여 샘플 앱을 <xref:index#preprocessor-directives-in-sample-code>구성하는 방법에 대한 자세한 내용은 을 참조하십시오.

## <a name="secret-storage-in-the-development-environment"></a>개발 환경의 비밀 저장소

[비밀 관리자 도구를](xref:security/app-secrets)사용하여 로컬로 비밀을 설정합니다. 개발 환경에서 로컬 컴퓨터에서 샘플 앱이 실행되면 로컬 비밀 관리자 저장소에서 암호가 로드됩니다.

비밀 관리자 도구는 `<UserSecretsId>` 앱의 프로젝트 파일에 있는 속성이 필요합니다. 속성 값 ()`{GUID}`을 고유한 GUID로 설정합니다.

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

암호는 이름 값 쌍으로 만들어집니다. 계층적 값(구성 섹션)은 `:` ASP.NET Core [구성](xref:fundamentals/configuration/index) 키 이름에서 구분 기호로 (콜론)을 사용합니다.

비밀 관리자는 프로젝트의 [콘텐츠 루트에](xref:fundamentals/index#content-root)열려 있는 명령 셸에서 `{SECRET NAME}` 사용 되며 `{SECRET VALUE}` 이름은 이며 값은 다음과 같습니다.

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

프로젝트 의 [콘텐츠 루트에서](xref:fundamentals/index#content-root) 명령 셸에서 다음 명령을 실행하여 샘플 앱의 비밀을 설정합니다.

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

이러한 암호가 Azure 키 자격 증명 모음섹션을 [사용하여 프로덕션 환경의 비밀 저장소에](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure 키 자격 증명 모음에 저장되면 `_dev` 접미사가 `_prod`로 변경됩니다. 접미사는 구성 값의 소스를 나타내는 앱출력에 시각적 신호를 제공합니다.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure 키 자격 증명 모음을 통해 프로덕션 환경의 비밀 저장소

빠른 시작에서 제공하는 [지침: Azure CLI 항목을 사용하여 Azure 키 볼트에서 비밀을 설정하고 검색하는](/azure/key-vault/quick-create-cli) 방법은 Azure 키 볼트를 만들고 샘플 앱에서 사용하는 비밀을 저장하기 위해 여기에 요약되어 있습니다. 자세한 내용은 이 항목을 참조하십시오.

1. [Azure 포털에서](https://portal.azure.com/)다음 방법 중 하나를 사용하여 Azure Cloud 셸을 엽니다.

   * 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. 텍스트 상자에서 검색 문자열 "Azure CLI"를 사용합니다.
   * 시작 클라우드 쉘 버튼으로 브라우저에서 **클라우드 쉘을 엽니다.**
   * Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.

   자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure 클라우드 셸의 개요를](/azure/cloud-shell/overview)참조하십시오.

1. 아직 인증되지 않은 경우 명령으로 로그인합니다. `az login`

1. 새 리소스 그룹에 대한 리소스 `{RESOURCE GROUP NAME}` 그룹 이름은 Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹을 `{LOCATION}` 만듭니다.

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 새 키 자격 증명 모음의 이름이며 `{KEY VAULT NAME}` `{LOCATION}` Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹에 키 자격 증명 모음을 만듭니다.

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 키 자격 증명 모음에 이름 값 쌍으로 비밀을 만듭니다.

   Azure 키 볼트 보안 검색대 보안 검색대 비밀 이름은 사용 문자 및 대시로 제한됩니다. 계층적 값(구성 섹션)은 `--` 구분 기호로 (두 개의 대시)를 사용합니다. 일반적으로 [ASP.NET 코어 구성의](xref:fundamentals/configuration/index)하위 키에서 섹션을 구분하는 데 사용되는 콜론은 키 볼트 보안 모음 보안 설정 에서 허용되지 않습니다. 따라서 비밀이 앱의 구성에 로드될 때 두 개의 대시가 사용되고 콜론으로 교환됩니다.

   다음 비밀은 샘플 앱과 함께 사용할 수 있습니다. 이 값에는 `_prod` 개발 환경에 로드된 `_dev` 접미사 값과 사용자 암호로 구분하는 접미사가 포함됩니다. 이전 `{KEY VAULT NAME}` 단계에서 만든 키 자격 증명 모음의 이름으로 바꿉니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure 호스팅이 아닌 앱에 응용 프로그램 ID 및 X.509 인증서 사용

Azure AD, Azure 키 자격 증명 모음 및 앱을 구성하여 Azure Active Directory 응용 프로그램 ID 및 X.509 인증서를 사용하여 **앱이 Azure 외부에서 호스팅될 때**키 자격 증명을 인증하도록 구성합니다. 자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.

> [!NOTE]
> Azure에서 호스팅되는 앱에 대해 응용 프로그램 ID 및 X.509 인증서를 사용하는 것이 지원되지만 Azure에서 앱을 호스팅할 때 [Azure 리소스에 대해 관리되는 ID를](#use-managed-identities-for-azure-resources) 사용하는 것이 좋습니다. 관리되는 ID는 앱이나 개발 환경에 인증서를 저장할 필요가 없습니다.

샘플 앱은 *Program.cs* 파일 의 맨 위에 있는 `#define` 명령문이 로 설정되면 응용 `Certificate`프로그램 ID 및 X.509 인증서를 사용합니다.

1. PKCS#12*아카이브(.pfx)* 인증서를 만듭니다. 인증서를 만드는 옵션에는 Windows및 [OpenSSL에서](https://www.openssl.org/) [MakeCert가](/windows/desktop/seccrypto/makecert) 있습니다.
1. 현재 사용자의 개인 인증서 저장소에 인증서를 설치합니다. 키를 내보낼 수 있는 것으로 표시하는 것은 선택 사항입니다. 이 프로세스의 나중에 사용되는 인증서의 지문에 유의하십시오.
1. PKCS#12*아카이브(.pfx)* 인증서를 DER 인코딩*인증서(.cer)로*내보냅니다.
1. Azure AD(앱**등록)로 앱을 등록합니다.**
1. DER 인코딩된*인증서(.cer)를*Azure AD에 업로드합니다.
   1. Azure AD에서 앱을 선택합니다.
   1. 인증서 **& 비밀로**이동합니다.
   1. **인증서 업로드를** 선택하여 공개 키가 포함된 인증서를 업로드합니다. *.cer*, *.pem*또는 *.crt* 인증서를 사용할 수 있습니다.
1. 키 볼트 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 *appsettings.json* 파일에 저장합니다.
1. Azure 포털의 **키 자격 증명 모음으로 이동합니다.**
1. Azure 키 자격 증명 모음 섹션을 [통해 프로덕션 환경의 비밀 저장소에서](#secret-storage-in-the-production-environment-with-azure-key-vault) 만든 키 자격 증명 모음을 선택합니다.
1. **액세스 정책을**선택합니다.
1. **액세스 정책 추가를**선택합니다.
1. **비밀 권한을** 열고 **앱에 Get** 및 **List** 권한을 제공합니다.
1. **보안 주체 선택을** 선택하고 등록된 앱을 이름으로 선택합니다. **선택** 단추를 누릅니다.
1. **확인**을 선택합니다.
1. **저장**을 선택합니다.
1. 앱을 배포합니다.

샘플 `Certificate` 앱은 비밀 이름과 `IConfigurationRoot` 동일한 이름으로 구성 값을 가져옵니다.

* 비계층적 값: 에 대한 `SecretName` 값은 `config["SecretName"]`에서 가져옵니다.
* 계층 적 값 (섹션): `:` 사용 (콜론) `GetSection` 표기법 또는 확장 메서드. 다음 방법 중 하나를 사용하여 구성 값을 얻습니다.
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 인증서는 OS에서 관리합니다. <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* 파일에서 제공 하는 값으로 응용 프로그램 호출:

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

예제 값:

* 키 볼트 이름:`contosovault`
* 응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* 인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다. 개발 환경에서 보안 값은 접미사와 함께 로드됩니다. `_dev` 프로덕션 환경에서 값은 접미사와 `_prod` 함께 로드됩니다.

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 관리되는 ID 사용

**Azure에 배포된 앱은** [Azure 리소스에 대한 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview)활용할 수 있으며, 이를 통해 앱은 앱에 저장된 자격 증명(응용 프로그램 ID 및 암호/클라이언트 보안)없이 Azure AD 인증을 사용하여 Azure Key Vault로 인증할 수 있습니다.

샘플 앱은 *Program.cs* 파일 의 `#define` 맨 위에 있는 명령문이 로 `Managed`설정된 경우 Azure 리소스에 대해 관리되는 ID를 사용합니다.

앱의 *appsettings.json* 파일에 볼트 이름을 입력합니다. 샘플 앱은 `Managed` 버전으로 설정할 때 응용 프로그램 ID와 암호(클라이언트 보안)가 필요하지 않으므로 이러한 구성 항목을 무시할 수 있습니다. 앱은 Azure에 배포되고 Azure는 *appsettings.json* 파일에 저장된 볼트 이름을 사용하여 Azure Key Vault에 액세스하도록 앱을 인증합니다.

샘플 앱을 Azure 앱 서비스에 배포합니다.

Azure 앱 서비스에 배포된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록됩니다. 다음 명령에서 사용할 배포에서 개체 ID를 가져옵니다. 개체 ID는 앱 서비스의 **ID** 패널에 있는 Azure 포털에 표시됩니다.

Azure CLI 및 앱의 개체 ID를 사용하여 `list` `get` 앱에 키 자격 증명 모음에 액세스할 수 있는 권한및 권한을 제공합니다.

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI, PowerShell 또는 Azure 포털을 사용하여 **앱을 다시 시작합니다.**

샘플 앱:

* 연결 문자열 없이 `AzureServiceTokenProvider` 클래스의 인스턴스를 만듭니다. 연결 문자열이 제공되지 않으면 공급자는 Azure 리소스에 대한 관리되는 ID에서 액세스 토큰을 가져오려고 시도합니다.
* 인스턴스 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 토큰 콜백을 통해 새 새 가 만들어집니다. `AzureServiceTokenProvider`
* 인스턴스는 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 모든 비밀 값을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 로드하고 키 이름의`:`콜론()으로`--`이중 대시()를 대체하는 기본 구현과 함께 사용됩니다.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

키 볼트 이름 예제 값:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다. 개발 환경에서 비밀 값은 `_dev` 사용자 암호에서 제공되므로 접미사가 있습니다. 프로덕션 환경에서 는 Azure 키 `_prod` 자격 증명 모음에서 제공 되므로 값이 접미사와 함께 로드 됩니다.

`Access denied` 오류가 발생하면 앱이 Azure AD에 등록되어 있고 키 자격 증명 모음에 대한 액세스 권한을 제공했는지 확인합니다. Azure에서 서비스를 다시 시작했는지 확인합니다.

관리되는 ID 및 Azure DevOps 파이프라인을 사용하여 공급자를 사용하는 방법에 대한 자세한 내용은 [관리되는 서비스 ID를 사용하여 VM에 대한 Azure 리소스 관리자 서비스 연결 만들기를](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)참조하십시오.

## <a name="configuration-options"></a>구성 옵션

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>다음을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>수락할 수 있습니다.

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| 속성         | Description |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>을 사용하여 값을 검색합니다. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>인스턴스는 비밀 로딩을 제어하는 데 사용됩니다. |
| `ReloadInterval` | `Timespan`을 사용하여 키 자격 증명 모음에서 변경 내용을 폴링하는 동안 기다립니다. 기본값은(구성이 `null` 다시 로드되지 않습니다)입니다. |
| `Vault`          | 키 볼트 URI. |

## <a name="use-a-key-name-prefix"></a>키 이름 접두사 사용

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는 의 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>허용하는 오버로드를 제공하므로 키 볼트 암호가 구성 키로 변환되는 방법을 제어할 수 있습니다. 예를 들어 앱 시작 시 제공한 접두사 값을 기반으로 비밀 값을 로드하는 인터페이스를 구현할 수 있습니다. 예를 들어 앱 버전에 따라 비밀을 로드할 수 있습니다.

> [!WARNING]
> 키 볼트 비밀에 접두사를 사용하여 여러 앱에 대한 비밀을 동일한 키 자격 증명 모음에 배치하거나 환경 비밀(예: *개발* 및 *생산* 비밀)을 동일한 자격 증명 모음에 배치하지 마십시오. 다른 앱 및 개발/프로덕션 환경에서는 별도의 키 자격 증명 모음을 사용하여 최고 수준의 보안을 위해 앱 환경을 격리하는 것이 좋습니다.

다음 예제에서는 키 자격 증명 모음에 보안 검색대(및 개발 환경에 대한 `5000-AppSecret` 비밀 관리자 도구 사용)에 대한 보안 설정(키 자격 증명 모음 보안 모음 보안 설정 이름에마침이 허용되지 않음)에 대해 설정됩니다. 이 비밀은 앱의 버전 5.0.0.0에 대한 앱 비밀을 나타냅니다. 앱의 다른 버전인 5.1.0.0의 경우 에 대한 키 자격 증명 모음(및 `5100-AppSecret`비밀 관리자 도구 사용)에 비밀이 추가됩니다. 각 앱 버전은 해당 버전의 비밀 `AppSecret`값을 해당 구성에 로드하여 비밀을 로드할 때 버전을 제거합니다.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정으로 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>호출됩니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

구현구성에 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 적절 한 비밀을 로드 하는 비밀의 버전 접두사에 반응 :

* `Load`은 그 이름이 접두사로 시작될 때 비밀을 로드합니다. 다른 비밀은 로드되지 않습니다.
* `GetKey`:
  * 비밀 이름에서 접두사를 제거합니다.
  * 모든 이름의 두 대시를 구성에 `KeyDelimiter`사용되는 구분 기호(일반적으로 콜론)로 바꿉니다. Azure 키 볼트는 비밀 이름에 콜론을 허용하지 않습니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

이 `Load` 메서드는 버전 접두사가 있는 메서드를 찾기 위해 볼트 비밀을 통해 이터레이션하는 공급자 알고리즘에 의해 호출됩니다. `Load`에서 버전 접두사가 발견되면 알고리즘은 `GetKey` 메서드를 사용하여 비밀 이름의 구성 이름을 반환합니다. 비밀의 이름에서 버전 접두사를 제거하고 앱의 구성 이름-값 쌍으로 로드하기 위한 나머지 비밀 이름을 반환합니다.

이 접근 방식이 구현되는 경우:

1. 앱의 프로젝트 파일에 지정된 앱 버전입니다. 다음 예제에서는 앱의 버전이 다음으로 `5.0.0.0`설정됩니다.

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>` 속성이 앱의 프로젝트 파일에 있는지 확인합니다. `{GUID}`

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [비밀 관리자 도구를](xref:security/app-secrets)사용하여 다음 비밀을 로컬로 저장합니다.

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 비밀은 다음 Azure CLI 명령을 사용하여 Azure 키 자격 증명 모음에 저장됩니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 앱이 실행되면 키 자격 증명 모음 암호가 로드됩니다. 문자열 `5000-AppSecret` 비밀은 앱의 프로젝트 파일()에`5.0.0.0`지정된 앱 버전과 일치합니다.

1. 대시가 `5000` 있는 버전은 키 이름에서 제거됩니다. 앱 전체에서 키로 `AppSecret` 구성을 읽으면 비밀 값이 로드됩니다.

1. 프로젝트 파일에서 앱 버전이 `5.1.0.0` 변경되고 앱이 다시 실행되면 반환되는 비밀 `5.1.0.0_secret_value_dev` 값이 개발 `5.1.0.0_secret_value_prod` 환경및 프로덕션에 있습니다.

> [!NOTE]
> 에 고유한 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>제공할 수도 있습니다. 사용자 지정 클라이언트는 앱 전체에서 클라이언트의 단일 인스턴스를 공유할 수 있도록 허용합니다.

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.

키가`:`콜론() 구분 기호를 포함할 수 있도록 하는 구성 소스에서 읽을 때 숫자 키 세그먼트는`:0:` `:1:`배열을 구성하는 키를 &hellip; `:{n}:`구별하는 데 사용됩니다. 자세한 내용은 [구성: 배열을 클래스에 바인딩합니다.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Azure 키 볼트 키는 콜론을 구분 기호로 사용할 수 없습니다. 이 항목에서 설명하는 접근 방식은`--`계층적 값(섹션)에 대한 구분 기호로 이중 대시()를 사용합니다. 배열`--0--`키는 이중 대시 및 숫자 키 세그먼트 (, `--1--` &hellip; `--{n}--`) 가 있는 Azure 키 볼트에 저장됩니다.

JSON 파일에서 제공하는 다음 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검사합니다. `WriteTo` 로깅 출력에 대한 대상을 설명하는 두 개의 Serilog *싱크를*반영하는 배열에 정의된 두 가지 개체 리터럴이 있습니다.

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

앞의 JSON 파일에 표시된 구성은 이중 대시()`--`표기및 숫자 세그먼트를 사용하여 Azure Key Vault에 저장됩니다.

| 키 | 값 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>비밀 다시 로드

암호가 호출될 `IConfigurationRoot.Reload()` 때까지 캐시됩니다. 키 자격 증명 모음의 만료됨, 비활성화된 및 업데이트된 암호는 실행될 때까지 `Reload` 앱에서 적용되지 않습니다.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>비활성화 및 만료된 비밀

비활성화 및 만료된 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>암호는 을 throw합니다. 앱이 throw되지 않도록 하려면 다른 구성 공급자를 사용하여 구성을 제공하거나 비활성화되거나 만료된 비밀을 업데이트합니다.

## <a name="troubleshoot"></a>문제 해결

앱이 공급자를 사용하여 구성을 로드하지 못하면 ASP.NET [코어 로깅 인프라에](xref:fundamentals/logging/index)오류 메시지가 기록됩니다. 다음 조건은 구성을 로드하지 못하게 합니다.

* Azure Active Directory에서 앱 또는 인증서가 올바르게 구성되지 않았습니다.
* Azure 키 자격 증명 모음에 키 자격 증명 모음이 없습니다.
* 앱이 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.
* 액세스 정책에는 `Get` `List` 사용 권한이 포함되지 않습니다.
* 키 자격 증명 모음에서 구성 데이터(이름-값 쌍)의 이름이 잘못 지정되거나 누락되었거나 비활성화되거나 만료됩니다.
* 앱에 잘못된 키 자격`KeyVaultName`증명 모음 이름 (), Azure AD 응용 프로그램 ID ()`AzureADApplicationId`또는 Azure AD 인증서 지문 ()이`AzureADCertThumbprint`있습니다.
* 앱에서 로드하려는 값에 대해 구성 키(이름)가 올바르지 않습니다.
* 앱의 액세스 정책을 키 자격 증명 모음에 추가할 때 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추를 선택하지 않았습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/index>
* [마이크로소프트 Azure: 키 볼트](https://azure.microsoft.com/services/key-vault/)
* [마이크로소프트 Azure: 키 볼트 문서](/azure/key-vault/)
* [Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법](/azure/key-vault/key-vault-hsm-protected-keys)
* [키볼트클라이언트 클래스](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-net)
* [자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 Microsoft [Azure 키 볼트](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용하여 Azure 키 볼트 암호에서 앱 구성 값을 로드하는 방법을 설명합니다. Azure Key Vault는 앱 및 서비스에서 사용하는 암호화 키 및 비밀을 보호하는 데 도움을 주는 클라우드 기반 서비스입니다. ASP.NET 핵심 앱과 함께 Azure 키 볼트를 사용하기 위한 일반적인 시나리오는 다음과 같습니다.

* 중요한 구성 데이터에 대한 액세스를 제어합니다.
* 구성 데이터를 저장할 때 FIPS 140-2 수준 2 검증된 하드웨어 보안 모듈(HSM)에 대한 요구 사항을 충족합니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [방법)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>패키지

[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 패키지 참조를 추가합니다.

## <a name="sample-app"></a>샘플 앱

샘플 앱은 *Program.cs* 파일 맨 위에 `#define` 있는 문으로 결정된 두 가지 모드 중 하나에서 실행됩니다.

* `Certificate`&ndash; Azure 키 볼트 클라이언트 ID 및 X.509 인증서를 사용하여 Azure 키 자격 증명 모음에 저장된 암호에 액세스하는 방법을 보여 줍니다. 이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 호스트에 배포된 모든 위치에서 실행할 수 있습니다.
* `Managed`&ndash; [Azure 리소스에 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 앱의 코드 또는 구성에 저장된 자격 증명 없이 Azure AD 인증을 사용하여 Azure Key Vault에 앱을 인증하는 방법을 보여 줍니다. 관리되는 ID를 사용하여 인증하는 경우 Azure AD 응용 프로그램 ID 및 암호(클라이언트 보안)가 필요하지 않습니다. 샘플 `Managed` 의 버전을 Azure에 배포해야 합니다. [Azure 리소스에 대한 관리되는 ID 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.

프로세서 전 지시문 ()`#define`을 사용하여 샘플 앱을 <xref:index#preprocessor-directives-in-sample-code>구성하는 방법에 대한 자세한 내용은 을 참조하십시오.

## <a name="secret-storage-in-the-development-environment"></a>개발 환경의 비밀 저장소

[비밀 관리자 도구를](xref:security/app-secrets)사용하여 로컬로 비밀을 설정합니다. 개발 환경에서 로컬 컴퓨터에서 샘플 앱이 실행되면 로컬 비밀 관리자 저장소에서 암호가 로드됩니다.

비밀 관리자 도구는 `<UserSecretsId>` 앱의 프로젝트 파일에 있는 속성이 필요합니다. 속성 값 ()`{GUID}`을 고유한 GUID로 설정합니다.

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

암호는 이름 값 쌍으로 만들어집니다. 계층적 값(구성 섹션)은 `:` ASP.NET Core [구성](xref:fundamentals/configuration/index) 키 이름에서 구분 기호로 (콜론)을 사용합니다.

비밀 관리자는 프로젝트의 [콘텐츠 루트에](xref:fundamentals/index#content-root)열려 있는 명령 셸에서 `{SECRET NAME}` 사용 되며 `{SECRET VALUE}` 이름은 이며 값은 다음과 같습니다.

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

프로젝트 의 [콘텐츠 루트에서](xref:fundamentals/index#content-root) 명령 셸에서 다음 명령을 실행하여 샘플 앱의 비밀을 설정합니다.

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

이러한 암호가 Azure 키 자격 증명 모음섹션을 [사용하여 프로덕션 환경의 비밀 저장소에](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure 키 자격 증명 모음에 저장되면 `_dev` 접미사가 `_prod`로 변경됩니다. 접미사는 구성 값의 소스를 나타내는 앱출력에 시각적 신호를 제공합니다.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure 키 자격 증명 모음을 통해 프로덕션 환경의 비밀 저장소

빠른 시작에서 제공하는 [지침: Azure CLI 항목을 사용하여 Azure 키 볼트에서 비밀을 설정하고 검색하는](/azure/key-vault/quick-create-cli) 방법은 Azure 키 볼트를 만들고 샘플 앱에서 사용하는 비밀을 저장하기 위해 여기에 요약되어 있습니다. 자세한 내용은 이 항목을 참조하십시오.

1. [Azure 포털에서](https://portal.azure.com/)다음 방법 중 하나를 사용하여 Azure Cloud 셸을 엽니다.

   * 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. 텍스트 상자에서 검색 문자열 "Azure CLI"를 사용합니다.
   * 시작 클라우드 쉘 버튼으로 브라우저에서 **클라우드 쉘을 엽니다.**
   * Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.

   자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure 클라우드 셸의 개요를](/azure/cloud-shell/overview)참조하십시오.

1. 아직 인증되지 않은 경우 명령으로 로그인합니다. `az login`

1. 새 리소스 그룹에 대한 리소스 `{RESOURCE GROUP NAME}` 그룹 이름은 Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹을 `{LOCATION}` 만듭니다.

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 새 키 자격 증명 모음의 이름이며 `{KEY VAULT NAME}` `{LOCATION}` Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹에 키 자격 증명 모음을 만듭니다.

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 키 자격 증명 모음에 이름 값 쌍으로 비밀을 만듭니다.

   Azure 키 볼트 보안 검색대 보안 검색대 비밀 이름은 사용 문자 및 대시로 제한됩니다. 계층적 값(구성 섹션)은 `--` 구분 기호로 (두 개의 대시)를 사용합니다. 일반적으로 [ASP.NET 코어 구성의](xref:fundamentals/configuration/index)하위 키에서 섹션을 구분하는 데 사용되는 콜론은 키 볼트 보안 모음 보안 설정 에서 허용되지 않습니다. 따라서 비밀이 앱의 구성에 로드될 때 두 개의 대시가 사용되고 콜론으로 교환됩니다.

   다음 비밀은 샘플 앱과 함께 사용할 수 있습니다. 이 값에는 `_prod` 개발 환경에 로드된 `_dev` 접미사 값과 사용자 암호로 구분하는 접미사가 포함됩니다. 이전 `{KEY VAULT NAME}` 단계에서 만든 키 자격 증명 모음의 이름으로 바꿉니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure 호스팅이 아닌 앱에 응용 프로그램 ID 및 X.509 인증서 사용

Azure AD, Azure 키 자격 증명 모음 및 앱을 구성하여 Azure Active Directory 응용 프로그램 ID 및 X.509 인증서를 사용하여 **앱이 Azure 외부에서 호스팅될 때**키 자격 증명을 인증하도록 구성합니다. 자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.

> [!NOTE]
> Azure에서 호스팅되는 앱에 대해 응용 프로그램 ID 및 X.509 인증서를 사용하는 것이 지원되지만 Azure에서 앱을 호스팅할 때 [Azure 리소스에 대해 관리되는 ID를](#use-managed-identities-for-azure-resources) 사용하는 것이 좋습니다. 관리되는 ID는 앱이나 개발 환경에 인증서를 저장할 필요가 없습니다.

샘플 앱은 *Program.cs* 파일 의 맨 위에 있는 `#define` 명령문이 로 설정되면 응용 `Certificate`프로그램 ID 및 X.509 인증서를 사용합니다.

1. PKCS#12*아카이브(.pfx)* 인증서를 만듭니다. 인증서를 만드는 옵션에는 Windows및 [OpenSSL에서](https://www.openssl.org/) [MakeCert가](/windows/desktop/seccrypto/makecert) 있습니다.
1. 현재 사용자의 개인 인증서 저장소에 인증서를 설치합니다. 키를 내보낼 수 있는 것으로 표시하는 것은 선택 사항입니다. 이 프로세스의 나중에 사용되는 인증서의 지문에 유의하십시오.
1. PKCS#12*아카이브(.pfx)* 인증서를 DER 인코딩*인증서(.cer)로*내보냅니다.
1. Azure AD(앱**등록)로 앱을 등록합니다.**
1. DER 인코딩된*인증서(.cer)를*Azure AD에 업로드합니다.
   1. Azure AD에서 앱을 선택합니다.
   1. 인증서 **& 비밀로**이동합니다.
   1. **인증서 업로드를** 선택하여 공개 키가 포함된 인증서를 업로드합니다. *.cer*, *.pem*또는 *.crt* 인증서를 사용할 수 있습니다.
1. 키 볼트 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 *appsettings.json* 파일에 저장합니다.
1. Azure 포털의 **키 자격 증명 모음으로 이동합니다.**
1. Azure 키 자격 증명 모음 섹션을 [통해 프로덕션 환경의 비밀 저장소에서](#secret-storage-in-the-production-environment-with-azure-key-vault) 만든 키 자격 증명 모음을 선택합니다.
1. **액세스 정책을**선택합니다.
1. **액세스 정책 추가를**선택합니다.
1. **비밀 권한을** 열고 **앱에 Get** 및 **List** 권한을 제공합니다.
1. **보안 주체 선택을** 선택하고 등록된 앱을 이름으로 선택합니다. **선택** 단추를 누릅니다.
1. **확인**을 선택합니다.
1. **저장**을 선택합니다.
1. 앱을 배포합니다.

샘플 `Certificate` 앱은 비밀 이름과 `IConfigurationRoot` 동일한 이름으로 구성 값을 가져옵니다.

* 비계층적 값: 에 대한 `SecretName` 값은 `config["SecretName"]`에서 가져옵니다.
* 계층 적 값 (섹션): `:` 사용 (콜론) `GetSection` 표기법 또는 확장 메서드. 다음 방법 중 하나를 사용하여 구성 값을 얻습니다.
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 인증서는 OS에서 관리합니다. <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* 파일에서 제공 하는 값으로 응용 프로그램 호출:

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

예제 값:

* 키 볼트 이름:`contosovault`
* 응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* 인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다. 개발 환경에서 보안 값은 접미사와 함께 로드됩니다. `_dev` 프로덕션 환경에서 값은 접미사와 `_prod` 함께 로드됩니다.

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 관리되는 ID 사용

**Azure에 배포된 앱은** [Azure 리소스에 대한 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview)활용할 수 있으며, 이를 통해 앱은 앱에 저장된 자격 증명(응용 프로그램 ID 및 암호/클라이언트 보안)없이 Azure AD 인증을 사용하여 Azure Key Vault로 인증할 수 있습니다.

샘플 앱은 *Program.cs* 파일 의 `#define` 맨 위에 있는 명령문이 로 `Managed`설정된 경우 Azure 리소스에 대해 관리되는 ID를 사용합니다.

앱의 *appsettings.json* 파일에 볼트 이름을 입력합니다. 샘플 앱은 `Managed` 버전으로 설정할 때 응용 프로그램 ID와 암호(클라이언트 보안)가 필요하지 않으므로 이러한 구성 항목을 무시할 수 있습니다. 앱은 Azure에 배포되고 Azure는 *appsettings.json* 파일에 저장된 볼트 이름을 사용하여 Azure Key Vault에 액세스하도록 앱을 인증합니다.

샘플 앱을 Azure 앱 서비스에 배포합니다.

Azure 앱 서비스에 배포된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록됩니다. 다음 명령에서 사용할 배포에서 개체 ID를 가져옵니다. 개체 ID는 앱 서비스의 **ID** 패널에 있는 Azure 포털에 표시됩니다.

Azure CLI 및 앱의 개체 ID를 사용하여 `list` `get` 앱에 키 자격 증명 모음에 액세스할 수 있는 권한및 권한을 제공합니다.

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI, PowerShell 또는 Azure 포털을 사용하여 **앱을 다시 시작합니다.**

샘플 앱:

* 연결 문자열 없이 `AzureServiceTokenProvider` 클래스의 인스턴스를 만듭니다. 연결 문자열이 제공되지 않으면 공급자는 Azure 리소스에 대한 관리되는 ID에서 액세스 토큰을 가져오려고 시도합니다.
* 인스턴스 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 토큰 콜백을 통해 새 새 가 만들어집니다. `AzureServiceTokenProvider`
* 인스턴스는 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 모든 비밀 값을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 로드하고 키 이름의`:`콜론()으로`--`이중 대시()를 대체하는 기본 구현과 함께 사용됩니다.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

키 볼트 이름 예제 값:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다. 개발 환경에서 비밀 값은 `_dev` 사용자 암호에서 제공되므로 접미사가 있습니다. 프로덕션 환경에서 는 Azure 키 `_prod` 자격 증명 모음에서 제공 되므로 값이 접미사와 함께 로드 됩니다.

`Access denied` 오류가 발생하면 앱이 Azure AD에 등록되어 있고 키 자격 증명 모음에 대한 액세스 권한을 제공했는지 확인합니다. Azure에서 서비스를 다시 시작했는지 확인합니다.

관리되는 ID 및 Azure DevOps 파이프라인을 사용하여 공급자를 사용하는 방법에 대한 자세한 내용은 [관리되는 서비스 ID를 사용하여 VM에 대한 Azure 리소스 관리자 서비스 연결 만들기를](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)참조하십시오.

## <a name="use-a-key-name-prefix"></a>키 이름 접두사 사용

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는 의 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>허용하는 오버로드를 제공하므로 키 볼트 암호가 구성 키로 변환되는 방법을 제어할 수 있습니다. 예를 들어 앱 시작 시 제공한 접두사 값을 기반으로 비밀 값을 로드하는 인터페이스를 구현할 수 있습니다. 예를 들어 앱 버전에 따라 비밀을 로드할 수 있습니다.

> [!WARNING]
> 키 볼트 비밀에 접두사를 사용하여 여러 앱에 대한 비밀을 동일한 키 자격 증명 모음에 배치하거나 환경 비밀(예: *개발* 및 *생산* 비밀)을 동일한 자격 증명 모음에 배치하지 마십시오. 다른 앱 및 개발/프로덕션 환경에서는 별도의 키 자격 증명 모음을 사용하여 최고 수준의 보안을 위해 앱 환경을 격리하는 것이 좋습니다.

다음 예제에서는 키 자격 증명 모음에 보안 검색대(및 개발 환경에 대한 `5000-AppSecret` 비밀 관리자 도구 사용)에 대한 보안 설정(키 자격 증명 모음 보안 모음 보안 설정 이름에마침이 허용되지 않음)에 대해 설정됩니다. 이 비밀은 앱의 버전 5.0.0.0에 대한 앱 비밀을 나타냅니다. 앱의 다른 버전인 5.1.0.0의 경우 에 대한 키 자격 증명 모음(및 `5100-AppSecret`비밀 관리자 도구 사용)에 비밀이 추가됩니다. 각 앱 버전은 해당 버전의 비밀 `AppSecret`값을 해당 구성에 로드하여 비밀을 로드할 때 버전을 제거합니다.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정으로 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>호출됩니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

구현구성에 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 적절 한 비밀을 로드 하는 비밀의 버전 접두사에 반응 :

* `Load`은 그 이름이 접두사로 시작될 때 비밀을 로드합니다. 다른 비밀은 로드되지 않습니다.
* `GetKey`:
  * 비밀 이름에서 접두사를 제거합니다.
  * 모든 이름의 두 대시를 구성에 `KeyDelimiter`사용되는 구분 기호(일반적으로 콜론)로 바꿉니다. Azure 키 볼트는 비밀 이름에 콜론을 허용하지 않습니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

이 `Load` 메서드는 버전 접두사가 있는 메서드를 찾기 위해 볼트 비밀을 통해 이터레이션하는 공급자 알고리즘에 의해 호출됩니다. `Load`에서 버전 접두사가 발견되면 알고리즘은 `GetKey` 메서드를 사용하여 비밀 이름의 구성 이름을 반환합니다. 비밀의 이름에서 버전 접두사를 제거하고 앱의 구성 이름-값 쌍으로 로드하기 위한 나머지 비밀 이름을 반환합니다.

이 접근 방식이 구현되는 경우:

1. 앱의 프로젝트 파일에 지정된 앱 버전입니다. 다음 예제에서는 앱의 버전이 다음으로 `5.0.0.0`설정됩니다.

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>` 속성이 앱의 프로젝트 파일에 있는지 확인합니다. `{GUID}`

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [비밀 관리자 도구를](xref:security/app-secrets)사용하여 다음 비밀을 로컬로 저장합니다.

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 비밀은 다음 Azure CLI 명령을 사용하여 Azure 키 자격 증명 모음에 저장됩니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 앱이 실행되면 키 자격 증명 모음 암호가 로드됩니다. 문자열 `5000-AppSecret` 비밀은 앱의 프로젝트 파일()에`5.0.0.0`지정된 앱 버전과 일치합니다.

1. 대시가 `5000` 있는 버전은 키 이름에서 제거됩니다. 앱 전체에서 키로 `AppSecret` 구성을 읽으면 비밀 값이 로드됩니다.

1. 프로젝트 파일에서 앱 버전이 `5.1.0.0` 변경되고 앱이 다시 실행되면 반환되는 비밀 `5.1.0.0_secret_value_dev` 값이 개발 `5.1.0.0_secret_value_prod` 환경및 프로덕션에 있습니다.

> [!NOTE]
> 에 고유한 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>제공할 수도 있습니다. 사용자 지정 클라이언트는 앱 전체에서 클라이언트의 단일 인스턴스를 공유할 수 있도록 허용합니다.

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.

키가`:`콜론() 구분 기호를 포함할 수 있도록 하는 구성 소스에서 읽을 때 숫자 키 세그먼트는`:0:` `:1:`배열을 구성하는 키를 &hellip; `:{n}:`구별하는 데 사용됩니다. 자세한 내용은 [구성: 배열을 클래스에 바인딩합니다.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Azure 키 볼트 키는 콜론을 구분 기호로 사용할 수 없습니다. 이 항목에서 설명하는 접근 방식은`--`계층적 값(섹션)에 대한 구분 기호로 이중 대시()를 사용합니다. 배열`--0--`키는 이중 대시 및 숫자 키 세그먼트 (, `--1--` &hellip; `--{n}--`) 가 있는 Azure 키 볼트에 저장됩니다.

JSON 파일에서 제공하는 다음 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검사합니다. `WriteTo` 로깅 출력에 대한 대상을 설명하는 두 개의 Serilog *싱크를*반영하는 배열에 정의된 두 가지 개체 리터럴이 있습니다.

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

앞의 JSON 파일에 표시된 구성은 이중 대시()`--`표기및 숫자 세그먼트를 사용하여 Azure Key Vault에 저장됩니다.

| 키 | 값 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>비밀 다시 로드

암호가 호출될 `IConfigurationRoot.Reload()` 때까지 캐시됩니다. 키 자격 증명 모음의 만료됨, 비활성화된 및 업데이트된 암호는 실행될 때까지 `Reload` 앱에서 적용되지 않습니다.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>비활성화 및 만료된 비밀

비활성화 및 만료된 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>암호는 을 throw합니다. 앱이 throw되지 않도록 하려면 다른 구성 공급자를 사용하여 구성을 제공하거나 비활성화되거나 만료된 비밀을 업데이트합니다.

## <a name="troubleshoot"></a>문제 해결

앱이 공급자를 사용하여 구성을 로드하지 못하면 ASP.NET [코어 로깅 인프라에](xref:fundamentals/logging/index)오류 메시지가 기록됩니다. 다음 조건은 구성을 로드하지 못하게 합니다.

* Azure Active Directory에서 앱 또는 인증서가 올바르게 구성되지 않았습니다.
* Azure 키 자격 증명 모음에 키 자격 증명 모음이 없습니다.
* 앱이 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.
* 액세스 정책에는 `Get` `List` 사용 권한이 포함되지 않습니다.
* 키 자격 증명 모음에서 구성 데이터(이름-값 쌍)의 이름이 잘못 지정되거나 누락되었거나 비활성화되거나 만료됩니다.
* 앱에 잘못된 키 자격`KeyVaultName`증명 모음 이름 (), Azure AD 응용 프로그램 ID ()`AzureADApplicationId`또는 Azure AD 인증서 지문 ()이`AzureADCertThumbprint`있습니다.
* 앱에서 로드하려는 값에 대해 구성 키(이름)가 올바르지 않습니다.
* 앱의 액세스 정책을 키 자격 증명 모음에 추가할 때 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추를 선택하지 않았습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/index>
* [마이크로소프트 Azure: 키 볼트](https://azure.microsoft.com/services/key-vault/)
* [마이크로소프트 Azure: 키 볼트 문서](/azure/key-vault/)
* [Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법](/azure/key-vault/key-vault-hsm-protected-keys)
* [키볼트클라이언트 클래스](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-net)
* [자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

