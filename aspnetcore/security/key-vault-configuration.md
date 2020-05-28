---
<span data-ttu-id="68816-101">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-102">'Blazor'</span></span>
- <span data-ttu-id="68816-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-103">'Identity'</span></span>
- <span data-ttu-id="68816-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-105">'Razor'</span></span>
- <span data-ttu-id="68816-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="68816-107">ASP.NET Core의 Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="68816-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="68816-108">[Andrew Stanton-간호사](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="68816-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68816-109">이 문서에서는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="68816-110">Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="68816-111">ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="68816-112">중요 한 구성 데이터에 대 한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="68816-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="68816-113">구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="68816-114">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68816-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="68816-115">패키지</span><span class="sxs-lookup"><span data-stu-id="68816-115">Packages</span></span>

<span data-ttu-id="68816-116">패키지 참조를 [Microsoft 확장명. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="68816-117">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="68816-117">Sample app</span></span>

<span data-ttu-id="68816-118">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="68816-119">`Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68816-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="68816-120">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="68816-121">`Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68816-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="68816-122">관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="68816-123">`Managed`샘플의 버전은 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="68816-124">[Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="68816-125">전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="68816-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="68816-126">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="68816-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="68816-127">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="68816-128">개발 환경에서 샘플 앱이 로컬 컴퓨터에서 실행 되 면 비밀이 로컬 암호 관리자 저장소에서 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="68816-129">비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="68816-130">속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="68816-131">비밀은 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="68816-132">계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="68816-133">암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="68816-134">프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="68816-135">이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` .</span><span class="sxs-lookup"><span data-stu-id="68816-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="68816-136">접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="68816-137">Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장</span><span class="sxs-lookup"><span data-stu-id="68816-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="68816-138">[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="68816-139">자세한 내용은 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="68816-140">[Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68816-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="68816-141">코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="68816-142">텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="68816-143">**Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68816-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="68816-144">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="68816-145">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="68816-146">아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="68816-147">다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="68816-148">다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="68816-149">키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="68816-150">Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="68816-151">계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="68816-152">일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="68816-153">따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="68816-154">다음 암호는 샘플 앱에서 사용 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="68816-155">값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="68816-156">`{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="68816-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="68816-157">Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="68816-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="68816-158">**앱이 azure 외부에서 호스팅될 때**Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="68816-159">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="68816-160">Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="68816-161">관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="68816-162">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="68816-163">PKCS # 12 archive (*.pfx*) 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="68816-164">인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="68816-165">현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="68816-166">키를 내보낼 수 있는 것으로 표시는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="68816-167">이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.</span><span class="sxs-lookup"><span data-stu-id="68816-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="68816-168">PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="68816-169">Azure AD (**앱 등록**)에 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="68816-170">DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="68816-171">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="68816-172">**인증서 & 암호**로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="68816-173">**인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="68816-174">*.Cer*, *pem*또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="68816-175">앱의 *appsettings* 파일에 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="68816-176">Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="68816-177">[Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="68816-178">**액세스 정책**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="68816-179">**액세스 정책 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="68816-180">**비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="68816-181">**보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="68816-182">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="68816-183">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-183">Select **OK**.</span></span>
1. <span data-ttu-id="68816-184">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-184">Select **Save**.</span></span>
1. <span data-ttu-id="68816-185">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-185">Deploy the app.</span></span>

<span data-ttu-id="68816-186">`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="68816-187">비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="68816-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="68816-188">계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="68816-189">다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="68816-190">X.509 인증서는 OS를 통해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="68816-191">응용 프로그램은 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings* 파일에서 제공 하는 값을 사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="68816-192">예제 값:</span><span class="sxs-lookup"><span data-stu-id="68816-192">Example values:</span></span>

* <span data-ttu-id="68816-193">키 자격 증명 모음 이름:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="68816-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="68816-194">응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="68816-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="68816-195">인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="68816-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="68816-196">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68816-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="68816-197">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="68816-198">개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="68816-199">프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="68816-200">Azure 리소스에 관리 되는 id 사용</span><span class="sxs-lookup"><span data-stu-id="68816-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="68816-201">**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="68816-202">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="68816-203">앱의 *appsettings json* 파일에 자격 증명 모음 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="68816-204">샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="68816-205">앱이 Azure에 배포 되 고 Azure는 *appsettings* 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="68816-206">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="68816-207">Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="68816-208">다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="68816-209">개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .</span><span class="sxs-lookup"><span data-stu-id="68816-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="68816-210">Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="68816-211">Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="68816-212">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="68816-212">The sample app:</span></span>

* <span data-ttu-id="68816-213">`AzureServiceTokenProvider`연결 문자열을 사용 하지 않고 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="68816-214">연결 문자열이 제공 되지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져오려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="68816-215"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스 토큰 콜백을 사용 하 여 새을 만듭니다 `AzureServiceTokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="68816-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="68816-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스는 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .</span><span class="sxs-lookup"><span data-stu-id="68816-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="68816-217">Key vault 이름 예 값:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="68816-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="68816-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68816-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="68816-219">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="68816-220">개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="68816-221">프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="68816-222">오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="68816-223">Azure에서 서비스를 다시 시작 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="68816-224">관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="68816-225">구성 옵션</span><span class="sxs-lookup"><span data-stu-id="68816-225">Configuration options</span></span>

<span data-ttu-id="68816-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>수락 가능 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="68816-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="68816-227">속성</span><span class="sxs-lookup"><span data-stu-id="68816-227">Property</span></span>         | <span data-ttu-id="68816-228">설명</span><span class="sxs-lookup"><span data-stu-id="68816-228">Description</span></span> |
| ---
<span data-ttu-id="68816-229">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-230">'Blazor'</span></span>
- <span data-ttu-id="68816-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-231">'Identity'</span></span>
- <span data-ttu-id="68816-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-233">'Razor'</span></span>
- <span data-ttu-id="68816-234">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-235">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-236">'Blazor'</span></span>
- <span data-ttu-id="68816-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-237">'Identity'</span></span>
- <span data-ttu-id="68816-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-239">'Razor'</span></span>
- <span data-ttu-id="68816-240">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-241">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-242">'Blazor'</span></span>
- <span data-ttu-id="68816-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-243">'Identity'</span></span>
- <span data-ttu-id="68816-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-245">'Razor'</span></span>
- <span data-ttu-id="68816-246">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-247">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-248">'Blazor'</span></span>
- <span data-ttu-id="68816-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-249">'Identity'</span></span>
- <span data-ttu-id="68816-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-251">'Razor'</span></span>
- <span data-ttu-id="68816-252">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-253">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-254">'Blazor'</span></span>
- <span data-ttu-id="68816-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-255">'Identity'</span></span>
- <span data-ttu-id="68816-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-257">'Razor'</span></span>
- <span data-ttu-id="68816-258">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-259">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-260">'Blazor'</span></span>
- <span data-ttu-id="68816-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-261">'Identity'</span></span>
- <span data-ttu-id="68816-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-263">'Razor'</span></span>
- <span data-ttu-id="68816-264">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-264">'SignalR' uid:</span></span> 

<span data-ttu-id="68816-265">-------- | ---제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-266">'Blazor'</span></span>
- <span data-ttu-id="68816-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-267">'Identity'</span></span>
- <span data-ttu-id="68816-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-269">'Razor'</span></span>
- <span data-ttu-id="68816-270">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-271">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-272">'Blazor'</span></span>
- <span data-ttu-id="68816-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-273">'Identity'</span></span>
- <span data-ttu-id="68816-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-275">'Razor'</span></span>
- <span data-ttu-id="68816-276">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68816-277">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68816-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68816-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68816-278">'Blazor'</span></span>
- <span data-ttu-id="68816-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68816-279">'Identity'</span></span>
- <span data-ttu-id="68816-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68816-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="68816-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68816-281">'Razor'</span></span>
- <span data-ttu-id="68816-282">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="68816-282">'SignalR' uid:</span></span> 

<span data-ttu-id="68816-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 값을 검색 하는 데 사용할입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="68816-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 비밀 로드를 제어 하는 데 사용 되는 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="68816-285">| | `ReloadInterval` | `Timespan` 변경에 대 한 주요 자격 증명 모음 폴링 시도 사이에 대기 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="68816-286">기본값은 `null` (구성이 다시 로드 되지 않음)입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="68816-287">| | `Vault`          | 키 자격 증명 모음 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="68816-288">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="68816-288">Use a key name prefix</span></span>

<span data-ttu-id="68816-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는의 구현을 허용 하는 오버 로드를 제공 하 여 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="68816-290">예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="68816-291">예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="68816-292">키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="68816-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="68816-293">앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="68816-294">다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="68816-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="68816-295">이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="68816-296">5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="68816-297">각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="68816-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정을 사용 하 여 호출 됩니다 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> .</span><span class="sxs-lookup"><span data-stu-id="68816-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="68816-299"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="68816-300">`Load`이름이 접두사로 시작 하는 경우 비밀을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="68816-301">다른 암호는 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="68816-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="68816-302">`GetKey`:</span></span>
  * <span data-ttu-id="68816-303">비밀 이름에서 접두사를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="68816-304">이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="68816-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="68816-305">Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="68816-306">`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="68816-307">에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="68816-308">암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="68816-309">이 방법이 구현 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="68816-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="68816-310">앱의 프로젝트 파일에 지정 된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="68816-311">다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="68816-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="68816-312">`<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="68816-313">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="68816-314">비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="68816-315">앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="68816-316">에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="68816-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="68816-317">`5000`(대시 포함) 버전이 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="68816-318">앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="68816-319">응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="68816-320">사용자 고유의 구현을 제공할 수도 있습니다 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="68816-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="68816-321">사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="68816-322">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="68816-322">Bind an array to a class</span></span>

<span data-ttu-id="68816-323">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="68816-324">키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="68816-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="68816-325">자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="68816-326">Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="68816-327">이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="68816-328">배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="68816-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="68816-329">JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="68816-330">`WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크*를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="68816-331">위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="68816-332">키</span><span class="sxs-lookup"><span data-stu-id="68816-332">Key</span></span> | <span data-ttu-id="68816-333">값</span><span class="sxs-lookup"><span data-stu-id="68816-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="68816-334">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="68816-334">Reload secrets</span></span>

<span data-ttu-id="68816-335">암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="68816-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="68816-336">키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .</span><span class="sxs-lookup"><span data-stu-id="68816-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="68816-337">비활성화 및 만료 된 비밀</span><span class="sxs-lookup"><span data-stu-id="68816-337">Disabled and expired secrets</span></span>

<span data-ttu-id="68816-338">비활성화 및 만료 된 비밀은을 throw <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="68816-339">앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="68816-340">문제 해결</span><span class="sxs-lookup"><span data-stu-id="68816-340">Troubleshoot</span></span>

<span data-ttu-id="68816-341">앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="68816-342">다음 조건에서는 구성을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="68816-343">앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="68816-344">키 자격 증명 모음이 Azure Key Vault에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="68816-345">앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="68816-346">액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .</span><span class="sxs-lookup"><span data-stu-id="68816-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="68816-347">키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="68816-348">앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 Id () `AzureADApplicationId` 또는 azure ad 인증서 지문 ( `AzureADCertThumbprint` )이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="68816-349">로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="68816-350">키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68816-351">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="68816-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="68816-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="68816-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="68816-353">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="68816-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="68816-354">Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법</span><span class="sxs-lookup"><span data-stu-id="68816-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="68816-355">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="68816-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="68816-356">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="68816-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="68816-357">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="68816-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68816-358">이 문서에서는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="68816-359">Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="68816-360">ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="68816-361">중요 한 구성 데이터에 대 한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="68816-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="68816-362">구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="68816-363">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68816-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="68816-364">패키지</span><span class="sxs-lookup"><span data-stu-id="68816-364">Packages</span></span>

<span data-ttu-id="68816-365">패키지 참조를 [Microsoft 확장명. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="68816-366">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="68816-366">Sample app</span></span>

<span data-ttu-id="68816-367">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="68816-368">`Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68816-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="68816-369">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="68816-370">`Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="68816-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="68816-371">관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="68816-372">`Managed`샘플의 버전은 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="68816-373">[Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="68816-374">전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="68816-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="68816-375">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="68816-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="68816-376">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="68816-377">개발 환경에서 샘플 앱이 로컬 컴퓨터에서 실행 되 면 비밀이 로컬 암호 관리자 저장소에서 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="68816-378">비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="68816-379">속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="68816-380">비밀은 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="68816-381">계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="68816-382">암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="68816-383">프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="68816-384">이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` .</span><span class="sxs-lookup"><span data-stu-id="68816-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="68816-385">접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="68816-386">Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장</span><span class="sxs-lookup"><span data-stu-id="68816-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="68816-387">[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="68816-388">자세한 내용은 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="68816-389">[Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68816-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="68816-390">코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="68816-391">텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="68816-392">**Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="68816-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="68816-393">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="68816-394">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="68816-395">아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="68816-396">다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="68816-397">다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="68816-398">키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="68816-399">Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="68816-400">계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="68816-401">일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="68816-402">따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="68816-403">다음 암호는 샘플 앱에서 사용 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="68816-404">값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="68816-405">`{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="68816-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="68816-406">Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="68816-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="68816-407">**앱이 azure 외부에서 호스팅될 때**Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="68816-408">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="68816-409">Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="68816-410">관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="68816-411">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="68816-412">PKCS # 12 archive (*.pfx*) 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="68816-413">인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="68816-414">현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="68816-415">키를 내보낼 수 있는 것으로 표시는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="68816-416">이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.</span><span class="sxs-lookup"><span data-stu-id="68816-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="68816-417">PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="68816-418">Azure AD (**앱 등록**)에 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="68816-419">DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="68816-420">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="68816-421">**인증서 & 암호**로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="68816-422">**인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="68816-423">*.Cer*, *pem*또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="68816-424">앱의 *appsettings* 파일에 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="68816-425">Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="68816-426">[Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="68816-427">**액세스 정책**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="68816-428">**액세스 정책 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="68816-429">**비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="68816-430">**보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="68816-431">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="68816-432">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-432">Select **OK**.</span></span>
1. <span data-ttu-id="68816-433">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-433">Select **Save**.</span></span>
1. <span data-ttu-id="68816-434">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-434">Deploy the app.</span></span>

<span data-ttu-id="68816-435">`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="68816-436">비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="68816-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="68816-437">계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="68816-438">다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="68816-439">X.509 인증서는 OS를 통해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="68816-440">응용 프로그램은 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings* 파일에서 제공 하는 값을 사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="68816-441">예제 값:</span><span class="sxs-lookup"><span data-stu-id="68816-441">Example values:</span></span>

* <span data-ttu-id="68816-442">키 자격 증명 모음 이름:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="68816-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="68816-443">응용 프로그램 ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="68816-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="68816-444">인증서 지문:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="68816-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="68816-445">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68816-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="68816-446">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="68816-447">개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="68816-448">프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="68816-449">Azure 리소스에 관리 되는 id 사용</span><span class="sxs-lookup"><span data-stu-id="68816-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="68816-450">**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="68816-451">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="68816-452">앱의 *appsettings json* 파일에 자격 증명 모음 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="68816-453">샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="68816-454">앱이 Azure에 배포 되 고 Azure는 *appsettings* 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="68816-455">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="68816-456">Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="68816-457">다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="68816-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="68816-458">개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .</span><span class="sxs-lookup"><span data-stu-id="68816-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="68816-459">Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="68816-460">Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="68816-461">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="68816-461">The sample app:</span></span>

* <span data-ttu-id="68816-462">`AzureServiceTokenProvider`연결 문자열을 사용 하지 않고 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="68816-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="68816-463">연결 문자열이 제공 되지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져오려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="68816-464"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스 토큰 콜백을 사용 하 여 새을 만듭니다 `AzureServiceTokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="68816-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="68816-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스는 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .</span><span class="sxs-lookup"><span data-stu-id="68816-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="68816-466">Key vault 이름 예 값:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="68816-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="68816-467">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68816-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="68816-468">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="68816-469">개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="68816-470">프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="68816-471">오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="68816-472">Azure에서 서비스를 다시 시작 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="68816-473">관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="68816-474">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="68816-474">Use a key name prefix</span></span>

<span data-ttu-id="68816-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>는의 구현을 허용 하는 오버 로드를 제공 하 여 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="68816-476">예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="68816-477">예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="68816-478">키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="68816-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="68816-479">앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="68816-480">다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="68816-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="68816-481">이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="68816-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="68816-482">5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="68816-483">각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="68816-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>사용자 지정을 사용 하 여 호출 됩니다 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> .</span><span class="sxs-lookup"><span data-stu-id="68816-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="68816-485"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="68816-486">`Load`이름이 접두사로 시작 하는 경우 비밀을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="68816-487">다른 암호는 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="68816-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="68816-488">`GetKey`:</span></span>
  * <span data-ttu-id="68816-489">비밀 이름에서 접두사를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="68816-490">이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="68816-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="68816-491">Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="68816-492">`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="68816-493">에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="68816-494">암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="68816-495">이 방법이 구현 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="68816-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="68816-496">앱의 프로젝트 파일에 지정 된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="68816-497">다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="68816-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="68816-498">`<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="68816-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="68816-499">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="68816-500">비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="68816-501">앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="68816-502">에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="68816-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="68816-503">`5000`(대시 포함) 버전이 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="68816-504">앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="68816-505">응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="68816-506">사용자 고유의 구현을 제공할 수도 있습니다 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="68816-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="68816-507">사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="68816-508">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="68816-508">Bind an array to a class</span></span>

<span data-ttu-id="68816-509">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="68816-510">키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="68816-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="68816-511">자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="68816-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="68816-512">Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="68816-513">이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="68816-514">배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="68816-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="68816-515">JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="68816-516">`WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크*를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="68816-517">위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="68816-518">키</span><span class="sxs-lookup"><span data-stu-id="68816-518">Key</span></span> | <span data-ttu-id="68816-519">값</span><span class="sxs-lookup"><span data-stu-id="68816-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="68816-520">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="68816-520">Reload secrets</span></span>

<span data-ttu-id="68816-521">암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="68816-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="68816-522">키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .</span><span class="sxs-lookup"><span data-stu-id="68816-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="68816-523">비활성화 및 만료 된 비밀</span><span class="sxs-lookup"><span data-stu-id="68816-523">Disabled and expired secrets</span></span>

<span data-ttu-id="68816-524">비활성화 및 만료 된 비밀은을 throw <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="68816-525">앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="68816-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="68816-526">문제 해결</span><span class="sxs-lookup"><span data-stu-id="68816-526">Troubleshoot</span></span>

<span data-ttu-id="68816-527">앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="68816-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="68816-528">다음 조건에서는 구성을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="68816-529">앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="68816-530">키 자격 증명 모음이 Azure Key Vault에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="68816-531">앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="68816-532">액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .</span><span class="sxs-lookup"><span data-stu-id="68816-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="68816-533">키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="68816-534">앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 Id () `AzureADApplicationId` 또는 azure ad 인증서 지문 ( `AzureADCertThumbprint` )이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="68816-535">로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="68816-536">키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="68816-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68816-537">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="68816-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="68816-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="68816-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="68816-539">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="68816-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="68816-540">Azure Key Vault에 대해 HSM 보호된 키를 생성하고 전송하는 방법</span><span class="sxs-lookup"><span data-stu-id="68816-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="68816-541">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="68816-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="68816-542">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="68816-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="68816-543">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="68816-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

