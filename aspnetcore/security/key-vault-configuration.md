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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="17f10-103">ASP.NET 코어의 Azure 키 볼트 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="17f10-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="17f10-104">[앤드류 스탠튼-간호사](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="17f10-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17f10-105">이 문서에서는 Microsoft [Azure 키 볼트](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용하여 Azure 키 볼트 암호에서 앱 구성 값을 로드하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="17f10-106">Azure Key Vault는 앱 및 서비스에서 사용하는 암호화 키 및 비밀을 보호하는 데 도움을 주는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="17f10-107">ASP.NET 핵심 앱과 함께 Azure 키 볼트를 사용하기 위한 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="17f10-108">중요한 구성 데이터에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="17f10-109">구성 데이터를 저장할 때 FIPS 140-2 수준 2 검증된 하드웨어 보안 모듈(HSM)에 대한 요구 사항을 충족합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="17f10-110">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="17f10-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="17f10-111">패키지</span><span class="sxs-lookup"><span data-stu-id="17f10-111">Packages</span></span>

<span data-ttu-id="17f10-112">[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="17f10-113">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="17f10-113">Sample app</span></span>

<span data-ttu-id="17f10-114">샘플 앱은 *Program.cs* 파일 맨 위에 `#define` 있는 문으로 결정된 두 가지 모드 중 하나에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="17f10-115">`Certificate`&ndash; Azure 키 볼트 클라이언트 ID 및 X.509 인증서를 사용하여 Azure 키 자격 증명 모음에 저장된 암호에 액세스하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="17f10-116">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 호스트에 배포된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="17f10-117">`Managed`&ndash; [Azure 리소스에 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 앱의 코드 또는 구성에 저장된 자격 증명 없이 Azure AD 인증을 사용하여 Azure Key Vault에 앱을 인증하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="17f10-118">관리되는 ID를 사용하여 인증하는 경우 Azure AD 응용 프로그램 ID 및 암호(클라이언트 보안)가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="17f10-119">샘플 `Managed` 의 버전을 Azure에 배포해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="17f10-120">[Azure 리소스에 대한 관리되는 ID 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="17f10-121">프로세서 전 지시문 ()`#define`을 사용하여 샘플 앱을 <xref:index#preprocessor-directives-in-sample-code>구성하는 방법에 대한 자세한 내용은 을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="17f10-122">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="17f10-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="17f10-123">[비밀 관리자 도구를](xref:security/app-secrets)사용하여 로컬로 비밀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="17f10-124">개발 환경에서 로컬 컴퓨터에서 샘플 앱이 실행되면 로컬 비밀 관리자 저장소에서 암호가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="17f10-125">비밀 관리자 도구는 `<UserSecretsId>` 앱의 프로젝트 파일에 있는 속성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="17f10-126">속성 값 ()`{GUID}`을 고유한 GUID로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="17f10-127">암호는 이름 값 쌍으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="17f10-128">계층적 값(구성 섹션)은 `:` ASP.NET Core [구성](xref:fundamentals/configuration/index) 키 이름에서 구분 기호로 (콜론)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="17f10-129">비밀 관리자는 프로젝트의 [콘텐츠 루트에](xref:fundamentals/index#content-root)열려 있는 명령 셸에서 `{SECRET NAME}` 사용 되며 `{SECRET VALUE}` 이름은 이며 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="17f10-130">프로젝트 의 [콘텐츠 루트에서](xref:fundamentals/index#content-root) 명령 셸에서 다음 명령을 실행하여 샘플 앱의 비밀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="17f10-131">이러한 암호가 Azure 키 자격 증명 모음섹션을 [사용하여 프로덕션 환경의 비밀 저장소에](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure 키 자격 증명 모음에 저장되면 `_dev` 접미사가 `_prod`로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="17f10-132">접미사는 구성 값의 소스를 나타내는 앱출력에 시각적 신호를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="17f10-133">Azure 키 자격 증명 모음을 통해 프로덕션 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="17f10-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="17f10-134">빠른 시작에서 제공하는 [지침: Azure CLI 항목을 사용하여 Azure 키 볼트에서 비밀을 설정하고 검색하는](/azure/key-vault/quick-create-cli) 방법은 Azure 키 볼트를 만들고 샘플 앱에서 사용하는 비밀을 저장하기 위해 여기에 요약되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="17f10-135">자세한 내용은 이 항목을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="17f10-136">[Azure 포털에서](https://portal.azure.com/)다음 방법 중 하나를 사용하여 Azure Cloud 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="17f10-137">코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="17f10-138">텍스트 상자에서 검색 문자열 "Azure CLI"를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="17f10-139">시작 클라우드 쉘 버튼으로 브라우저에서 **클라우드 쉘을 엽니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="17f10-140">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="17f10-141">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure 클라우드 셸의 개요를](/azure/cloud-shell/overview)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="17f10-142">아직 인증되지 않은 경우 명령으로 로그인합니다. `az login`</span><span class="sxs-lookup"><span data-stu-id="17f10-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="17f10-143">새 리소스 그룹에 대한 리소스 `{RESOURCE GROUP NAME}` 그룹 이름은 Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹을 `{LOCATION}` 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="17f10-144">새 키 자격 증명 모음의 이름이며 `{KEY VAULT NAME}` `{LOCATION}` Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹에 키 자격 증명 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="17f10-145">키 자격 증명 모음에 이름 값 쌍으로 비밀을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="17f10-146">Azure 키 볼트 보안 검색대 보안 검색대 비밀 이름은 사용 문자 및 대시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="17f10-147">계층적 값(구성 섹션)은 `--` 구분 기호로 (두 개의 대시)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="17f10-148">일반적으로 [ASP.NET 코어 구성의](xref:fundamentals/configuration/index)하위 키에서 섹션을 구분하는 데 사용되는 콜론은 키 볼트 보안 모음 보안 설정 에서 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="17f10-149">따라서 비밀이 앱의 구성에 로드될 때 두 개의 대시가 사용되고 콜론으로 교환됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="17f10-150">다음 비밀은 샘플 앱과 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="17f10-151">이 값에는 `_prod` 개발 환경에 로드된 `_dev` 접미사 값과 사용자 암호로 구분하는 접미사가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="17f10-152">이전 `{KEY VAULT NAME}` 단계에서 만든 키 자격 증명 모음의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="17f10-153">Azure 호스팅이 아닌 앱에 응용 프로그램 ID 및 X.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="17f10-154">Azure AD, Azure 키 자격 증명 모음 및 앱을 구성하여 Azure Active Directory 응용 프로그램 ID 및 X.509 인증서를 사용하여 **앱이 Azure 외부에서 호스팅될 때**키 자격 증명을 인증하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="17f10-155">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17f10-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="17f10-156">Azure에서 호스팅되는 앱에 대해 응용 프로그램 ID 및 X.509 인증서를 사용하는 것이 지원되지만 Azure에서 앱을 호스팅할 때 [Azure 리소스에 대해 관리되는 ID를](#use-managed-identities-for-azure-resources) 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="17f10-157">관리되는 ID는 앱이나 개발 환경에 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="17f10-158">샘플 앱은 *Program.cs* 파일 의 맨 위에 있는 `#define` 명령문이 로 설정되면 응용 `Certificate`프로그램 ID 및 X.509 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="17f10-159">PKCS#12*아카이브(.pfx)* 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="17f10-160">인증서를 만드는 옵션에는 Windows및 [OpenSSL에서](https://www.openssl.org/) [MakeCert가](/windows/desktop/seccrypto/makecert) 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="17f10-161">현재 사용자의 개인 인증서 저장소에 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="17f10-162">키를 내보낼 수 있는 것으로 표시하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="17f10-163">이 프로세스의 나중에 사용되는 인증서의 지문에 유의하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="17f10-164">PKCS#12*아카이브(.pfx)* 인증서를 DER 인코딩*인증서(.cer)로*내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="17f10-165">Azure AD(앱**등록)로 앱을 등록합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="17f10-166">DER 인코딩된*인증서(.cer)를*Azure AD에 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="17f10-167">Azure AD에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="17f10-168">인증서 **& 비밀로**이동합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="17f10-169">**인증서 업로드를** 선택하여 공개 키가 포함된 인증서를 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="17f10-170">*.cer*, *.pem*또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="17f10-171">키 볼트 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 *appsettings.json* 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="17f10-172">Azure 포털의 **키 자격 증명 모음으로 이동합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="17f10-173">Azure 키 자격 증명 모음 섹션을 [통해 프로덕션 환경의 비밀 저장소에서](#secret-storage-in-the-production-environment-with-azure-key-vault) 만든 키 자격 증명 모음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="17f10-174">**액세스 정책을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="17f10-175">**액세스 정책 추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="17f10-176">**비밀 권한을** 열고 **앱에 Get** 및 **List** 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="17f10-177">**보안 주체 선택을** 선택하고 등록된 앱을 이름으로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="17f10-178">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="17f10-179">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-179">Select **OK**.</span></span>
1. <span data-ttu-id="17f10-180">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-180">Select **Save**.</span></span>
1. <span data-ttu-id="17f10-181">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-181">Deploy the app.</span></span>

<span data-ttu-id="17f10-182">샘플 `Certificate` 앱은 비밀 이름과 `IConfigurationRoot` 동일한 이름으로 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="17f10-183">비계층적 값: 에 대한 `SecretName` 값은 `config["SecretName"]`에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="17f10-184">계층 적 값 (섹션): `:` 사용 (콜론) `GetSection` 표기법 또는 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="17f10-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="17f10-185">다음 방법 중 하나를 사용하여 구성 값을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="17f10-186">X.509 인증서는 OS에서 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="17f10-187"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* 파일에서 제공 하는 값으로 응용 프로그램 호출:</span><span class="sxs-lookup"><span data-stu-id="17f10-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="17f10-188">예제 값:</span><span class="sxs-lookup"><span data-stu-id="17f10-188">Example values:</span></span>

* <span data-ttu-id="17f10-189">키 볼트 이름:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="17f10-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="17f10-190">응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="17f10-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="17f10-191">인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="17f10-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="17f10-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="17f10-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="17f10-193">앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="17f10-194">개발 환경에서 보안 값은 접미사와 함께 로드됩니다. `_dev`</span><span class="sxs-lookup"><span data-stu-id="17f10-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="17f10-195">프로덕션 환경에서 값은 접미사와 `_prod` 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="17f10-196">Azure 리소스에 관리되는 ID 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="17f10-197">**Azure에 배포된 앱은** [Azure 리소스에 대한 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview)활용할 수 있으며, 이를 통해 앱은 앱에 저장된 자격 증명(응용 프로그램 ID 및 암호/클라이언트 보안)없이 Azure AD 인증을 사용하여 Azure Key Vault로 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="17f10-198">샘플 앱은 *Program.cs* 파일 의 `#define` 맨 위에 있는 명령문이 로 `Managed`설정된 경우 Azure 리소스에 대해 관리되는 ID를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="17f10-199">앱의 *appsettings.json* 파일에 볼트 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="17f10-200">샘플 앱은 `Managed` 버전으로 설정할 때 응용 프로그램 ID와 암호(클라이언트 보안)가 필요하지 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="17f10-201">앱은 Azure에 배포되고 Azure는 *appsettings.json* 파일에 저장된 볼트 이름을 사용하여 Azure Key Vault에 액세스하도록 앱을 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="17f10-202">샘플 앱을 Azure 앱 서비스에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="17f10-203">Azure 앱 서비스에 배포된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="17f10-204">다음 명령에서 사용할 배포에서 개체 ID를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="17f10-205">개체 ID는 앱 서비스의 **ID** 패널에 있는 Azure 포털에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="17f10-206">Azure CLI 및 앱의 개체 ID를 사용하여 `list` `get` 앱에 키 자격 증명 모음에 액세스할 수 있는 권한및 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="17f10-207">Azure CLI, PowerShell 또는 Azure 포털을 사용하여 **앱을 다시 시작합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="17f10-208">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="17f10-208">The sample app:</span></span>

* <span data-ttu-id="17f10-209">연결 문자열 없이 `AzureServiceTokenProvider` 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="17f10-210">연결 문자열이 제공되지 않으면 공급자는 Azure 리소스에 대한 관리되는 ID에서 액세스 토큰을 가져오려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="17f10-211">인스턴스 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 토큰 콜백을 통해 새 새 가 만들어집니다. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="17f10-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="17f10-212">인스턴스는 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 모든 비밀 값을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 로드하고 키 이름의`:`콜론()으로`--`이중 대시()를 대체하는 기본 구현과 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="17f10-213">키 볼트 이름 예제 값:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="17f10-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="17f10-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="17f10-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="17f10-215">앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="17f10-216">개발 환경에서 비밀 값은 `_dev` 사용자 암호에서 제공되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="17f10-217">프로덕션 환경에서 는 Azure 키 `_prod` 자격 증명 모음에서 제공 되므로 값이 접미사와 함께 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="17f10-218">`Access denied` 오류가 발생하면 앱이 Azure AD에 등록되어 있고 키 자격 증명 모음에 대한 액세스 권한을 제공했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="17f10-219">Azure에서 서비스를 다시 시작했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="17f10-220">관리되는 ID 및 Azure DevOps 파이프라인을 사용하여 공급자를 사용하는 방법에 대한 자세한 내용은 [관리되는 서비스 ID를 사용하여 VM에 대한 Azure 리소스 관리자 서비스 연결 만들기를](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="17f10-221">구성 옵션</span><span class="sxs-lookup"><span data-stu-id="17f10-221">Configuration options</span></span>

<span data-ttu-id="17f10-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>다음을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>수락할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="17f10-223">속성</span><span class="sxs-lookup"><span data-stu-id="17f10-223">Property</span></span>         | <span data-ttu-id="17f10-224">Description</span><span class="sxs-lookup"><span data-stu-id="17f10-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="17f10-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>을 사용하여 값을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="17f10-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>인스턴스는 비밀 로딩을 제어하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="17f10-227">`Timespan`을 사용하여 키 자격 증명 모음에서 변경 내용을 폴링하는 동안 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="17f10-228">기본값은(구성이 `null` 다시 로드되지 않습니다)입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="17f10-229">키 볼트 URI.</span><span class="sxs-lookup"><span data-stu-id="17f10-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="17f10-230">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-230">Use a key name prefix</span></span>

<span data-ttu-id="17f10-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는 의 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>허용하는 오버로드를 제공하므로 키 볼트 암호가 구성 키로 변환되는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="17f10-232">예를 들어 앱 시작 시 제공한 접두사 값을 기반으로 비밀 값을 로드하는 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="17f10-233">예를 들어 앱 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="17f10-234">키 볼트 비밀에 접두사를 사용하여 여러 앱에 대한 비밀을 동일한 키 자격 증명 모음에 배치하거나 환경 비밀(예: *개발* 및 *생산* 비밀)을 동일한 자격 증명 모음에 배치하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="17f10-235">다른 앱 및 개발/프로덕션 환경에서는 별도의 키 자격 증명 모음을 사용하여 최고 수준의 보안을 위해 앱 환경을 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="17f10-236">다음 예제에서는 키 자격 증명 모음에 보안 검색대(및 개발 환경에 대한 `5000-AppSecret` 비밀 관리자 도구 사용)에 대한 보안 설정(키 자격 증명 모음 보안 모음 보안 설정 이름에마침이 허용되지 않음)에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="17f10-237">이 비밀은 앱의 버전 5.0.0.0에 대한 앱 비밀을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="17f10-238">앱의 다른 버전인 5.1.0.0의 경우 에 대한 키 자격 증명 모음(및 `5100-AppSecret`비밀 관리자 도구 사용)에 비밀이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="17f10-239">각 앱 버전은 해당 버전의 비밀 `AppSecret`값을 해당 구성에 로드하여 비밀을 로드할 때 버전을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="17f10-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정으로 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="17f10-241">구현구성에 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 적절 한 비밀을 로드 하는 비밀의 버전 접두사에 반응 :</span><span class="sxs-lookup"><span data-stu-id="17f10-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="17f10-242">`Load`은 그 이름이 접두사로 시작될 때 비밀을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="17f10-243">다른 비밀은 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="17f10-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="17f10-244">`GetKey`:</span></span>
  * <span data-ttu-id="17f10-245">비밀 이름에서 접두사를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="17f10-246">모든 이름의 두 대시를 구성에 `KeyDelimiter`사용되는 구분 기호(일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="17f10-247">Azure 키 볼트는 비밀 이름에 콜론을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="17f10-248">이 `Load` 메서드는 버전 접두사가 있는 메서드를 찾기 위해 볼트 비밀을 통해 이터레이션하는 공급자 알고리즘에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="17f10-249">`Load`에서 버전 접두사가 발견되면 알고리즘은 `GetKey` 메서드를 사용하여 비밀 이름의 구성 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="17f10-250">비밀의 이름에서 버전 접두사를 제거하고 앱의 구성 이름-값 쌍으로 로드하기 위한 나머지 비밀 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="17f10-251">이 접근 방식이 구현되는 경우:</span><span class="sxs-lookup"><span data-stu-id="17f10-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="17f10-252">앱의 프로젝트 파일에 지정된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="17f10-253">다음 예제에서는 앱의 버전이 다음으로 `5.0.0.0`설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="17f10-254">`<UserSecretsId>` 속성이 앱의 프로젝트 파일에 있는지 확인합니다. `{GUID}`</span><span class="sxs-lookup"><span data-stu-id="17f10-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="17f10-255">[비밀 관리자 도구를](xref:security/app-secrets)사용하여 다음 비밀을 로컬로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="17f10-256">비밀은 다음 Azure CLI 명령을 사용하여 Azure 키 자격 증명 모음에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="17f10-257">앱이 실행되면 키 자격 증명 모음 암호가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="17f10-258">문자열 `5000-AppSecret` 비밀은 앱의 프로젝트 파일()에`5.0.0.0`지정된 앱 버전과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="17f10-259">대시가 `5000` 있는 버전은 키 이름에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="17f10-260">앱 전체에서 키로 `AppSecret` 구성을 읽으면 비밀 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="17f10-261">프로젝트 파일에서 앱 버전이 `5.1.0.0` 변경되고 앱이 다시 실행되면 반환되는 비밀 `5.1.0.0_secret_value_dev` 값이 개발 `5.1.0.0_secret_value_prod` 환경및 프로덕션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="17f10-262">에 고유한 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="17f10-263">사용자 지정 클라이언트는 앱 전체에서 클라이언트의 단일 인스턴스를 공유할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="17f10-264">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="17f10-264">Bind an array to a class</span></span>

<span data-ttu-id="17f10-265">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="17f10-266">키가`:`콜론() 구분 기호를 포함할 수 있도록 하는 구성 소스에서 읽을 때 숫자 키 세그먼트는`:0:` `:1:`배열을 구성하는 키를 &hellip; `:{n}:`구별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="17f10-267">자세한 내용은 [구성: 배열을 클래스에 바인딩합니다.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="17f10-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="17f10-268">Azure 키 볼트 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="17f10-269">이 항목에서 설명하는 접근 방식은`--`계층적 값(섹션)에 대한 구분 기호로 이중 대시()를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="17f10-270">배열`--0--`키는 이중 대시 및 숫자 키 세그먼트 (, `--1--` &hellip; `--{n}--`) 가 있는 Azure 키 볼트에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="17f10-271">JSON 파일에서 제공하는 다음 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="17f10-272">`WriteTo` 로깅 출력에 대한 대상을 설명하는 두 개의 Serilog *싱크를*반영하는 배열에 정의된 두 가지 개체 리터럴이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="17f10-273">앞의 JSON 파일에 표시된 구성은 이중 대시()`--`표기및 숫자 세그먼트를 사용하여 Azure Key Vault에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="17f10-274">키</span><span class="sxs-lookup"><span data-stu-id="17f10-274">Key</span></span> | <span data-ttu-id="17f10-275">값</span><span class="sxs-lookup"><span data-stu-id="17f10-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="17f10-276">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="17f10-276">Reload secrets</span></span>

<span data-ttu-id="17f10-277">암호가 호출될 `IConfigurationRoot.Reload()` 때까지 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="17f10-278">키 자격 증명 모음의 만료됨, 비활성화된 및 업데이트된 암호는 실행될 때까지 `Reload` 앱에서 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="17f10-279">비활성화 및 만료된 비밀</span><span class="sxs-lookup"><span data-stu-id="17f10-279">Disabled and expired secrets</span></span>

<span data-ttu-id="17f10-280">비활성화 및 만료된 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>암호는 을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="17f10-281">앱이 throw되지 않도록 하려면 다른 구성 공급자를 사용하여 구성을 제공하거나 비활성화되거나 만료된 비밀을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="17f10-282">문제 해결</span><span class="sxs-lookup"><span data-stu-id="17f10-282">Troubleshoot</span></span>

<span data-ttu-id="17f10-283">앱이 공급자를 사용하여 구성을 로드하지 못하면 ASP.NET [코어 로깅 인프라에](xref:fundamentals/logging/index)오류 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="17f10-284">다음 조건은 구성을 로드하지 못하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="17f10-285">Azure Active Directory에서 앱 또는 인증서가 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="17f10-286">Azure 키 자격 증명 모음에 키 자격 증명 모음이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="17f10-287">앱이 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="17f10-288">액세스 정책에는 `Get` `List` 사용 권한이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="17f10-289">키 자격 증명 모음에서 구성 데이터(이름-값 쌍)의 이름이 잘못 지정되거나 누락되었거나 비활성화되거나 만료됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="17f10-290">앱에 잘못된 키 자격`KeyVaultName`증명 모음 이름 (), Azure AD 응용 프로그램 ID ()`AzureADApplicationId`또는 Azure AD 인증서 지문 ()이`AzureADCertThumbprint`있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="17f10-291">앱에서 로드하려는 값에 대해 구성 키(이름)가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="17f10-292">앱의 액세스 정책을 키 자격 증명 모음에 추가할 때 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추를 선택하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17f10-293">추가 자료</span><span class="sxs-lookup"><span data-stu-id="17f10-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="17f10-294">마이크로소프트 Azure: 키 볼트</span><span class="sxs-lookup"><span data-stu-id="17f10-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="17f10-295">마이크로소프트 Azure: 키 볼트 문서</span><span class="sxs-lookup"><span data-stu-id="17f10-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="17f10-296">Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법</span><span class="sxs-lookup"><span data-stu-id="17f10-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="17f10-297">키볼트클라이언트 클래스</span><span class="sxs-lookup"><span data-stu-id="17f10-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="17f10-298">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="17f10-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="17f10-299">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="17f10-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17f10-300">이 문서에서는 Microsoft [Azure 키 볼트](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용하여 Azure 키 볼트 암호에서 앱 구성 값을 로드하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="17f10-301">Azure Key Vault는 앱 및 서비스에서 사용하는 암호화 키 및 비밀을 보호하는 데 도움을 주는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="17f10-302">ASP.NET 핵심 앱과 함께 Azure 키 볼트를 사용하기 위한 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="17f10-303">중요한 구성 데이터에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="17f10-304">구성 데이터를 저장할 때 FIPS 140-2 수준 2 검증된 하드웨어 보안 모듈(HSM)에 대한 요구 사항을 충족합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="17f10-305">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="17f10-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="17f10-306">패키지</span><span class="sxs-lookup"><span data-stu-id="17f10-306">Packages</span></span>

<span data-ttu-id="17f10-307">[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="17f10-308">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="17f10-308">Sample app</span></span>

<span data-ttu-id="17f10-309">샘플 앱은 *Program.cs* 파일 맨 위에 `#define` 있는 문으로 결정된 두 가지 모드 중 하나에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="17f10-310">`Certificate`&ndash; Azure 키 볼트 클라이언트 ID 및 X.509 인증서를 사용하여 Azure 키 자격 증명 모음에 저장된 암호에 액세스하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="17f10-311">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 처리할 수 있는 호스트에 배포된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="17f10-312">`Managed`&ndash; [Azure 리소스에 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview) 사용하여 앱의 코드 또는 구성에 저장된 자격 증명 없이 Azure AD 인증을 사용하여 Azure Key Vault에 앱을 인증하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="17f10-313">관리되는 ID를 사용하여 인증하는 경우 Azure AD 응용 프로그램 ID 및 암호(클라이언트 보안)가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="17f10-314">샘플 `Managed` 의 버전을 Azure에 배포해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="17f10-315">[Azure 리소스에 대한 관리되는 ID 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="17f10-316">프로세서 전 지시문 ()`#define`을 사용하여 샘플 앱을 <xref:index#preprocessor-directives-in-sample-code>구성하는 방법에 대한 자세한 내용은 을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="17f10-317">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="17f10-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="17f10-318">[비밀 관리자 도구를](xref:security/app-secrets)사용하여 로컬로 비밀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="17f10-319">개발 환경에서 로컬 컴퓨터에서 샘플 앱이 실행되면 로컬 비밀 관리자 저장소에서 암호가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="17f10-320">비밀 관리자 도구는 `<UserSecretsId>` 앱의 프로젝트 파일에 있는 속성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="17f10-321">속성 값 ()`{GUID}`을 고유한 GUID로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="17f10-322">암호는 이름 값 쌍으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="17f10-323">계층적 값(구성 섹션)은 `:` ASP.NET Core [구성](xref:fundamentals/configuration/index) 키 이름에서 구분 기호로 (콜론)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="17f10-324">비밀 관리자는 프로젝트의 [콘텐츠 루트에](xref:fundamentals/index#content-root)열려 있는 명령 셸에서 `{SECRET NAME}` 사용 되며 `{SECRET VALUE}` 이름은 이며 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="17f10-325">프로젝트 의 [콘텐츠 루트에서](xref:fundamentals/index#content-root) 명령 셸에서 다음 명령을 실행하여 샘플 앱의 비밀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="17f10-326">이러한 암호가 Azure 키 자격 증명 모음섹션을 [사용하여 프로덕션 환경의 비밀 저장소에](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure 키 자격 증명 모음에 저장되면 `_dev` 접미사가 `_prod`로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="17f10-327">접미사는 구성 값의 소스를 나타내는 앱출력에 시각적 신호를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="17f10-328">Azure 키 자격 증명 모음을 통해 프로덕션 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="17f10-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="17f10-329">빠른 시작에서 제공하는 [지침: Azure CLI 항목을 사용하여 Azure 키 볼트에서 비밀을 설정하고 검색하는](/azure/key-vault/quick-create-cli) 방법은 Azure 키 볼트를 만들고 샘플 앱에서 사용하는 비밀을 저장하기 위해 여기에 요약되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="17f10-330">자세한 내용은 이 항목을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="17f10-331">[Azure 포털에서](https://portal.azure.com/)다음 방법 중 하나를 사용하여 Azure Cloud 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="17f10-332">코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="17f10-333">텍스트 상자에서 검색 문자열 "Azure CLI"를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="17f10-334">시작 클라우드 쉘 버튼으로 브라우저에서 **클라우드 쉘을 엽니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="17f10-335">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="17f10-336">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure 클라우드 셸의 개요를](/azure/cloud-shell/overview)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="17f10-337">아직 인증되지 않은 경우 명령으로 로그인합니다. `az login`</span><span class="sxs-lookup"><span data-stu-id="17f10-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="17f10-338">새 리소스 그룹에 대한 리소스 `{RESOURCE GROUP NAME}` 그룹 이름은 Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹을 `{LOCATION}` 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="17f10-339">새 키 자격 증명 모음의 이름이며 `{KEY VAULT NAME}` `{LOCATION}` Azure 영역(데이터 센터)인 다음 명령을 사용하여 리소스 그룹에 키 자격 증명 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="17f10-340">키 자격 증명 모음에 이름 값 쌍으로 비밀을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="17f10-341">Azure 키 볼트 보안 검색대 보안 검색대 비밀 이름은 사용 문자 및 대시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="17f10-342">계층적 값(구성 섹션)은 `--` 구분 기호로 (두 개의 대시)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="17f10-343">일반적으로 [ASP.NET 코어 구성의](xref:fundamentals/configuration/index)하위 키에서 섹션을 구분하는 데 사용되는 콜론은 키 볼트 보안 모음 보안 설정 에서 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="17f10-344">따라서 비밀이 앱의 구성에 로드될 때 두 개의 대시가 사용되고 콜론으로 교환됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="17f10-345">다음 비밀은 샘플 앱과 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="17f10-346">이 값에는 `_prod` 개발 환경에 로드된 `_dev` 접미사 값과 사용자 암호로 구분하는 접미사가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="17f10-347">이전 `{KEY VAULT NAME}` 단계에서 만든 키 자격 증명 모음의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="17f10-348">Azure 호스팅이 아닌 앱에 응용 프로그램 ID 및 X.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="17f10-349">Azure AD, Azure 키 자격 증명 모음 및 앱을 구성하여 Azure Active Directory 응용 프로그램 ID 및 X.509 인증서를 사용하여 **앱이 Azure 외부에서 호스팅될 때**키 자격 증명을 인증하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="17f10-350">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17f10-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="17f10-351">Azure에서 호스팅되는 앱에 대해 응용 프로그램 ID 및 X.509 인증서를 사용하는 것이 지원되지만 Azure에서 앱을 호스팅할 때 [Azure 리소스에 대해 관리되는 ID를](#use-managed-identities-for-azure-resources) 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="17f10-352">관리되는 ID는 앱이나 개발 환경에 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="17f10-353">샘플 앱은 *Program.cs* 파일 의 맨 위에 있는 `#define` 명령문이 로 설정되면 응용 `Certificate`프로그램 ID 및 X.509 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="17f10-354">PKCS#12*아카이브(.pfx)* 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="17f10-355">인증서를 만드는 옵션에는 Windows및 [OpenSSL에서](https://www.openssl.org/) [MakeCert가](/windows/desktop/seccrypto/makecert) 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="17f10-356">현재 사용자의 개인 인증서 저장소에 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="17f10-357">키를 내보낼 수 있는 것으로 표시하는 것은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="17f10-358">이 프로세스의 나중에 사용되는 인증서의 지문에 유의하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="17f10-359">PKCS#12*아카이브(.pfx)* 인증서를 DER 인코딩*인증서(.cer)로*내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="17f10-360">Azure AD(앱**등록)로 앱을 등록합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="17f10-361">DER 인코딩된*인증서(.cer)를*Azure AD에 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="17f10-362">Azure AD에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="17f10-363">인증서 **& 비밀로**이동합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="17f10-364">**인증서 업로드를** 선택하여 공개 키가 포함된 인증서를 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="17f10-365">*.cer*, *.pem*또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="17f10-366">키 볼트 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 *appsettings.json* 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="17f10-367">Azure 포털의 **키 자격 증명 모음으로 이동합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="17f10-368">Azure 키 자격 증명 모음 섹션을 [통해 프로덕션 환경의 비밀 저장소에서](#secret-storage-in-the-production-environment-with-azure-key-vault) 만든 키 자격 증명 모음을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="17f10-369">**액세스 정책을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="17f10-370">**액세스 정책 추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="17f10-371">**비밀 권한을** 열고 **앱에 Get** 및 **List** 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="17f10-372">**보안 주체 선택을** 선택하고 등록된 앱을 이름으로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="17f10-373">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="17f10-374">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-374">Select **OK**.</span></span>
1. <span data-ttu-id="17f10-375">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-375">Select **Save**.</span></span>
1. <span data-ttu-id="17f10-376">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-376">Deploy the app.</span></span>

<span data-ttu-id="17f10-377">샘플 `Certificate` 앱은 비밀 이름과 `IConfigurationRoot` 동일한 이름으로 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="17f10-378">비계층적 값: 에 대한 `SecretName` 값은 `config["SecretName"]`에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="17f10-379">계층 적 값 (섹션): `:` 사용 (콜론) `GetSection` 표기법 또는 확장 메서드.</span><span class="sxs-lookup"><span data-stu-id="17f10-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="17f10-380">다음 방법 중 하나를 사용하여 구성 값을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="17f10-381">X.509 인증서는 OS에서 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="17f10-382"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* 파일에서 제공 하는 값으로 응용 프로그램 호출:</span><span class="sxs-lookup"><span data-stu-id="17f10-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="17f10-383">예제 값:</span><span class="sxs-lookup"><span data-stu-id="17f10-383">Example values:</span></span>

* <span data-ttu-id="17f10-384">키 볼트 이름:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="17f10-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="17f10-385">응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="17f10-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="17f10-386">인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="17f10-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="17f10-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="17f10-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="17f10-388">앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="17f10-389">개발 환경에서 보안 값은 접미사와 함께 로드됩니다. `_dev`</span><span class="sxs-lookup"><span data-stu-id="17f10-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="17f10-390">프로덕션 환경에서 값은 접미사와 `_prod` 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="17f10-391">Azure 리소스에 관리되는 ID 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="17f10-392">**Azure에 배포된 앱은** [Azure 리소스에 대한 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview)활용할 수 있으며, 이를 통해 앱은 앱에 저장된 자격 증명(응용 프로그램 ID 및 암호/클라이언트 보안)없이 Azure AD 인증을 사용하여 Azure Key Vault로 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="17f10-393">샘플 앱은 *Program.cs* 파일 의 `#define` 맨 위에 있는 명령문이 로 `Managed`설정된 경우 Azure 리소스에 대해 관리되는 ID를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="17f10-394">앱의 *appsettings.json* 파일에 볼트 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="17f10-395">샘플 앱은 `Managed` 버전으로 설정할 때 응용 프로그램 ID와 암호(클라이언트 보안)가 필요하지 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="17f10-396">앱은 Azure에 배포되고 Azure는 *appsettings.json* 파일에 저장된 볼트 이름을 사용하여 Azure Key Vault에 액세스하도록 앱을 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="17f10-397">샘플 앱을 Azure 앱 서비스에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="17f10-398">Azure 앱 서비스에 배포된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="17f10-399">다음 명령에서 사용할 배포에서 개체 ID를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="17f10-400">개체 ID는 앱 서비스의 **ID** 패널에 있는 Azure 포털에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="17f10-401">Azure CLI 및 앱의 개체 ID를 사용하여 `list` `get` 앱에 키 자격 증명 모음에 액세스할 수 있는 권한및 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="17f10-402">Azure CLI, PowerShell 또는 Azure 포털을 사용하여 **앱을 다시 시작합니다.**</span><span class="sxs-lookup"><span data-stu-id="17f10-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="17f10-403">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="17f10-403">The sample app:</span></span>

* <span data-ttu-id="17f10-404">연결 문자열 없이 `AzureServiceTokenProvider` 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="17f10-405">연결 문자열이 제공되지 않으면 공급자는 Azure 리소스에 대한 관리되는 ID에서 액세스 토큰을 가져오려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="17f10-406">인스턴스 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 토큰 콜백을 통해 새 새 가 만들어집니다. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="17f10-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="17f10-407">인스턴스는 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 모든 비밀 값을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 로드하고 키 이름의`:`콜론()으로`--`이중 대시()를 대체하는 기본 구현과 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="17f10-408">키 볼트 이름 예제 값:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="17f10-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="17f10-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="17f10-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="17f10-410">앱을 실행하면 웹 페이지에 로드된 비밀 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="17f10-411">개발 환경에서 비밀 값은 `_dev` 사용자 암호에서 제공되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="17f10-412">프로덕션 환경에서 는 Azure 키 `_prod` 자격 증명 모음에서 제공 되므로 값이 접미사와 함께 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="17f10-413">`Access denied` 오류가 발생하면 앱이 Azure AD에 등록되어 있고 키 자격 증명 모음에 대한 액세스 권한을 제공했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="17f10-414">Azure에서 서비스를 다시 시작했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="17f10-415">관리되는 ID 및 Azure DevOps 파이프라인을 사용하여 공급자를 사용하는 방법에 대한 자세한 내용은 [관리되는 서비스 ID를 사용하여 VM에 대한 Azure 리소스 관리자 서비스 연결 만들기를](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="17f10-416">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="17f10-416">Use a key name prefix</span></span>

<span data-ttu-id="17f10-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는 의 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>허용하는 오버로드를 제공하므로 키 볼트 암호가 구성 키로 변환되는 방법을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="17f10-418">예를 들어 앱 시작 시 제공한 접두사 값을 기반으로 비밀 값을 로드하는 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="17f10-419">예를 들어 앱 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="17f10-420">키 볼트 비밀에 접두사를 사용하여 여러 앱에 대한 비밀을 동일한 키 자격 증명 모음에 배치하거나 환경 비밀(예: *개발* 및 *생산* 비밀)을 동일한 자격 증명 모음에 배치하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="17f10-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="17f10-421">다른 앱 및 개발/프로덕션 환경에서는 별도의 키 자격 증명 모음을 사용하여 최고 수준의 보안을 위해 앱 환경을 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="17f10-422">다음 예제에서는 키 자격 증명 모음에 보안 검색대(및 개발 환경에 대한 `5000-AppSecret` 비밀 관리자 도구 사용)에 대한 보안 설정(키 자격 증명 모음 보안 모음 보안 설정 이름에마침이 허용되지 않음)에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="17f10-423">이 비밀은 앱의 버전 5.0.0.0에 대한 앱 비밀을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="17f10-424">앱의 다른 버전인 5.1.0.0의 경우 에 대한 키 자격 증명 모음(및 `5100-AppSecret`비밀 관리자 도구 사용)에 비밀이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="17f10-425">각 앱 버전은 해당 버전의 비밀 `AppSecret`값을 해당 구성에 로드하여 비밀을 로드할 때 버전을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="17f10-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정으로 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="17f10-427">구현구성에 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 적절 한 비밀을 로드 하는 비밀의 버전 접두사에 반응 :</span><span class="sxs-lookup"><span data-stu-id="17f10-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="17f10-428">`Load`은 그 이름이 접두사로 시작될 때 비밀을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="17f10-429">다른 비밀은 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="17f10-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="17f10-430">`GetKey`:</span></span>
  * <span data-ttu-id="17f10-431">비밀 이름에서 접두사를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="17f10-432">모든 이름의 두 대시를 구성에 `KeyDelimiter`사용되는 구분 기호(일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="17f10-433">Azure 키 볼트는 비밀 이름에 콜론을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="17f10-434">이 `Load` 메서드는 버전 접두사가 있는 메서드를 찾기 위해 볼트 비밀을 통해 이터레이션하는 공급자 알고리즘에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="17f10-435">`Load`에서 버전 접두사가 발견되면 알고리즘은 `GetKey` 메서드를 사용하여 비밀 이름의 구성 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="17f10-436">비밀의 이름에서 버전 접두사를 제거하고 앱의 구성 이름-값 쌍으로 로드하기 위한 나머지 비밀 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="17f10-437">이 접근 방식이 구현되는 경우:</span><span class="sxs-lookup"><span data-stu-id="17f10-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="17f10-438">앱의 프로젝트 파일에 지정된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="17f10-439">다음 예제에서는 앱의 버전이 다음으로 `5.0.0.0`설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="17f10-440">`<UserSecretsId>` 속성이 앱의 프로젝트 파일에 있는지 확인합니다. `{GUID}`</span><span class="sxs-lookup"><span data-stu-id="17f10-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="17f10-441">[비밀 관리자 도구를](xref:security/app-secrets)사용하여 다음 비밀을 로컬로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="17f10-442">비밀은 다음 Azure CLI 명령을 사용하여 Azure 키 자격 증명 모음에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="17f10-443">앱이 실행되면 키 자격 증명 모음 암호가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="17f10-444">문자열 `5000-AppSecret` 비밀은 앱의 프로젝트 파일()에`5.0.0.0`지정된 앱 버전과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="17f10-445">대시가 `5000` 있는 버전은 키 이름에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="17f10-446">앱 전체에서 키로 `AppSecret` 구성을 읽으면 비밀 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="17f10-447">프로젝트 파일에서 앱 버전이 `5.1.0.0` 변경되고 앱이 다시 실행되면 반환되는 비밀 `5.1.0.0_secret_value_dev` 값이 개발 `5.1.0.0_secret_value_prod` 환경및 프로덕션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="17f10-448">에 고유한 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 구현을 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="17f10-449">사용자 지정 클라이언트는 앱 전체에서 클라이언트의 단일 인스턴스를 공유할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="17f10-450">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="17f10-450">Bind an array to a class</span></span>

<span data-ttu-id="17f10-451">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="17f10-452">키가`:`콜론() 구분 기호를 포함할 수 있도록 하는 구성 소스에서 읽을 때 숫자 키 세그먼트는`:0:` `:1:`배열을 구성하는 키를 &hellip; `:{n}:`구별하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="17f10-453">자세한 내용은 [구성: 배열을 클래스에 바인딩합니다.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="17f10-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="17f10-454">Azure 키 볼트 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="17f10-455">이 항목에서 설명하는 접근 방식은`--`계층적 값(섹션)에 대한 구분 기호로 이중 대시()를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="17f10-456">배열`--0--`키는 이중 대시 및 숫자 키 세그먼트 (, `--1--` &hellip; `--{n}--`) 가 있는 Azure 키 볼트에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="17f10-457">JSON 파일에서 제공하는 다음 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="17f10-458">`WriteTo` 로깅 출력에 대한 대상을 설명하는 두 개의 Serilog *싱크를*반영하는 배열에 정의된 두 가지 개체 리터럴이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="17f10-459">앞의 JSON 파일에 표시된 구성은 이중 대시()`--`표기및 숫자 세그먼트를 사용하여 Azure Key Vault에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="17f10-460">키</span><span class="sxs-lookup"><span data-stu-id="17f10-460">Key</span></span> | <span data-ttu-id="17f10-461">값</span><span class="sxs-lookup"><span data-stu-id="17f10-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="17f10-462">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="17f10-462">Reload secrets</span></span>

<span data-ttu-id="17f10-463">암호가 호출될 `IConfigurationRoot.Reload()` 때까지 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="17f10-464">키 자격 증명 모음의 만료됨, 비활성화된 및 업데이트된 암호는 실행될 때까지 `Reload` 앱에서 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="17f10-465">비활성화 및 만료된 비밀</span><span class="sxs-lookup"><span data-stu-id="17f10-465">Disabled and expired secrets</span></span>

<span data-ttu-id="17f10-466">비활성화 및 만료된 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>암호는 을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="17f10-467">앱이 throw되지 않도록 하려면 다른 구성 공급자를 사용하여 구성을 제공하거나 비활성화되거나 만료된 비밀을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="17f10-468">문제 해결</span><span class="sxs-lookup"><span data-stu-id="17f10-468">Troubleshoot</span></span>

<span data-ttu-id="17f10-469">앱이 공급자를 사용하여 구성을 로드하지 못하면 ASP.NET [코어 로깅 인프라에](xref:fundamentals/logging/index)오류 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="17f10-470">다음 조건은 구성을 로드하지 못하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="17f10-471">Azure Active Directory에서 앱 또는 인증서가 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="17f10-472">Azure 키 자격 증명 모음에 키 자격 증명 모음이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="17f10-473">앱이 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="17f10-474">액세스 정책에는 `Get` `List` 사용 권한이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="17f10-475">키 자격 증명 모음에서 구성 데이터(이름-값 쌍)의 이름이 잘못 지정되거나 누락되었거나 비활성화되거나 만료됩니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="17f10-476">앱에 잘못된 키 자격`KeyVaultName`증명 모음 이름 (), Azure AD 응용 프로그램 ID ()`AzureADApplicationId`또는 Azure AD 인증서 지문 ()이`AzureADCertThumbprint`있습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="17f10-477">앱에서 로드하려는 값에 대해 구성 키(이름)가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="17f10-478">앱의 액세스 정책을 키 자격 증명 모음에 추가할 때 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추를 선택하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="17f10-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17f10-479">추가 자료</span><span class="sxs-lookup"><span data-stu-id="17f10-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="17f10-480">마이크로소프트 Azure: 키 볼트</span><span class="sxs-lookup"><span data-stu-id="17f10-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="17f10-481">마이크로소프트 Azure: 키 볼트 문서</span><span class="sxs-lookup"><span data-stu-id="17f10-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="17f10-482">Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법</span><span class="sxs-lookup"><span data-stu-id="17f10-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="17f10-483">키볼트클라이언트 클래스</span><span class="sxs-lookup"><span data-stu-id="17f10-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="17f10-484">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="17f10-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="17f10-485">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="17f10-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

