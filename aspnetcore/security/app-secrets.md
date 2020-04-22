---
title: ASP.NET 코어에서 개발 에 응용 프로그램 비밀의 안전한 저장
author: rick-anderson
description: ASP.NET Core 앱을 개발하는 동안 중요한 정보를 앱 비밀로 저장하고 검색하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791601"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="56106-103">ASP.NET 코어에서 개발 에 응용 프로그램 비밀의 안전한 저장</span><span class="sxs-lookup"><span data-stu-id="56106-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56106-104">[릭 앤더슨,](https://twitter.com/RickAndMSFT) [커크 라킨,](https://twitter.com/serpent5) [다니엘 로스,](https://github.com/danroth27) [스콧 애디](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="56106-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="56106-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56106-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="56106-106">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발하는 동안 중요한 데이터를 저장하고 검색하는 기술을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="56106-107">암호 또는 기타 중요한 데이터를 소스 코드에 저장하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="56106-108">프로덕션 비밀은 개발 또는 테스트에 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="56106-109">비밀은 앱과 함께 배포해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="56106-110">대신 환경 변수, Azure 키 볼트 등과 같은 제어된 수단을 통해 프로덕션 환경에서 비밀을 사용할 수 있어야 합니다. [Azure Key Vault 구성 공급자를](xref:security/key-vault-configuration)통해 Azure 테스트 및 프로덕션 비밀을 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="56106-111">환경 변수</span><span class="sxs-lookup"><span data-stu-id="56106-111">Environment variables</span></span>

<span data-ttu-id="56106-112">환경 변수는 코드 또는 로컬 구성 파일에 앱 비밀의 저장을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="56106-113">환경 변수는 이전에 지정된 모든 구성 원본에 대한 구성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="56106-114">**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="56106-115">기본 데이터베이스 연결 문자열은 키와 `DefaultConnection`함께 프로젝트의 *appsettings.json* 파일에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="56106-116">기본 연결 문자열은 사용자 모드에서 실행되며 암호가 필요하지 않은 LocalDB용입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="56106-117">앱 배포 중에 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="56106-118">환경 변수는 중요한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="56106-119">환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="56106-120">컴퓨터 또는 프로세스가 손상된 경우 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="56106-121">사용자 기밀의 공개를 방지하기 위한 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="56106-122">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="56106-122">Secret Manager</span></span>

<span data-ttu-id="56106-123">비밀 관리자 도구는 ASP.NET 핵심 프로젝트를 개발하는 동안 중요한 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="56106-124">이 컨텍스트에서 중요한 데이터는 앱 보안 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="56106-125">앱 암호는 프로젝트 트리와 별도의 위치에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="56106-126">앱 비밀은 특정 프로젝트와 연결되거나 여러 프로젝트에서 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="56106-127">앱 비밀이 소스 제어에 체크 인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="56106-128">비밀 관리자 도구는 저장된 암호를 암호화하지 않으며 신뢰할 수 있는 저장소로 취급해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="56106-129">그것은 단지 개발 목적을 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-129">It's for development purposes only.</span></span> <span data-ttu-id="56106-130">키와 값은 사용자 프로필 디렉터리에서 JSON 구성 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="56106-131">시크릿 관리자 도구의 작동 방식</span><span class="sxs-lookup"><span data-stu-id="56106-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="56106-132">비밀 관리자 도구는 값이 저장되는 위치와 방법과 같은 구현 세부 정보를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="56106-133">이러한 구현 세부 정보를 알지 못해도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="56106-134">값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 JSON 구성 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="56106-135">Windows</span><span class="sxs-lookup"><span data-stu-id="56106-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="56106-136">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="56106-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="56106-137">리눅스 / 맥OS</span><span class="sxs-lookup"><span data-stu-id="56106-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="56106-138">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="56106-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="56106-139">앞의 파일 경로에서 `<user_secrets_id>` *.csproj* 파일에 지정된 `UserSecretsId` 값으로 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="56106-140">비밀 관리자 도구로 저장된 데이터의 위치 나 형식에 따라 코드를 작성하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="56106-141">이러한 구현 세부 정보는 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-141">These implementation details may change.</span></span> <span data-ttu-id="56106-142">예를 들어 보안 값은 암호화되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="56106-143">비밀 저장 사용</span><span class="sxs-lookup"><span data-stu-id="56106-143">Enable secret storage</span></span>

<span data-ttu-id="56106-144">비밀 관리자 도구는 사용자 프로필에 저장된 프로젝트별 구성 설정에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="56106-145">비밀 관리자 도구에는 `init` .NET 코어 SDK 3.0.100 이상의 명령이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="56106-146">사용자 비밀을 사용하려면 프로젝트 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="56106-147">앞의 명령은 `UserSecretsId` *.csproj* 파일 `PropertyGroup` 내에 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="56106-148">기본적으로 내부 `UserSecretsId` 텍스트는 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="56106-149">내부 텍스트는 임의이지만 프로젝트에 고유합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="56106-150">Visual Studio에서 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **사용자 암호 관리를** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="56106-151">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="56106-152">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="56106-152">Set a secret</span></span>

<span data-ttu-id="56106-153">키와 해당 값으로 구성된 앱 암호를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="56106-154">비밀은 프로젝트의 `UserSecretsId` 값과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="56106-155">예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="56106-156">앞의 예제에서 콜론은 `Movies` `ServiceApiKey` 속성이 있는 개체 리터럴을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56106-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="56106-157">비밀 관리자 도구도 다른 디렉토리에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="56106-158">`--project` 옵션을 사용하여 *.csproj* 파일이 있는 파일 시스템 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="56106-159">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="56106-160">비주얼 스튜디오에서 JSON 구조 평탄화</span><span class="sxs-lookup"><span data-stu-id="56106-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="56106-161">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *secrets.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="56106-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="56106-162">*secrets.json의* 내용을 저장할 키 값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56106-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="56106-163">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="56106-164">JSON 구조를 통해 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`수정 후 평평해져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="56106-165">예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"` 실행하면 `Movies` 개체가 리터럴로 축소됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="56106-166">수정된 파일은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="56106-167">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="56106-167">Set multiple secrets</span></span>

<span data-ttu-id="56106-168">`set` JSON을 명령으로 파이핑하여 일괄 처리를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="56106-169">다음 예제에서는 *input.json* 파일의 내용을 `set` 명령에 파이프합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="56106-170">Windows</span><span class="sxs-lookup"><span data-stu-id="56106-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="56106-171">명령 셸을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="56106-172">리눅스 / 맥OS</span><span class="sxs-lookup"><span data-stu-id="56106-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="56106-173">명령 셸을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="56106-174">비밀에 액세스</span><span class="sxs-lookup"><span data-stu-id="56106-174">Access a secret</span></span>

<span data-ttu-id="56106-175">[ASP.NET 핵심 구성 API는](xref:fundamentals/configuration/index) 비밀 관리자 비밀에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="56106-176">프로젝트가 미리 구성된 기본값으로 호스트의 새 인스턴스를 초기화하기 위해 호출할 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 때 사용자 암호 구성 소스가 개발 모드에 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="56106-177">`CreateDefaultBuilder`있는 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 경우 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>호출 :</span><span class="sxs-lookup"><span data-stu-id="56106-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="56106-178">호출되지 않은 경우 `CreateDefaultBuilder` 을 호출하여 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>사용자 암호 구성 소스를 명시적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="56106-179">다음 `AddUserSecrets` 예제와 같이 앱이 개발 환경에서 실행되는 경우에만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="56106-180">사용자 암호는 API를 `Configuration` 통해 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="56106-181">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="56106-181">Map secrets to a POCO</span></span>

<span data-ttu-id="56106-182">전체 개체를 POCO(속성이 있는 간단한 .NET 클래스)에 매핑하는 것은 관련 속성을 집계하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-183">선행 비밀을 POCO에 매핑하려면 `Configuration` API의 개체 [그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="56106-184">다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 속성 값에 `ServiceApiKey` 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="56106-185">`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 비밀은 `MovieSettings`다음의 각 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="56106-186">비밀이 있는 문자열 교체</span><span class="sxs-lookup"><span data-stu-id="56106-186">String replacement with secrets</span></span>

<span data-ttu-id="56106-187">암호를 일반 텍스트로 저장하는 것은 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="56106-188">예를 들어 *appsettings.json에* 저장된 데이터베이스 연결 문자열에는 지정된 사용자의 암호가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="56106-189">보다 안전한 방법은 암호를 암호로 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="56106-190">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="56106-191">`Password` *appsettings.json의*연결 문자열에서 키-값 쌍을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="56106-192">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="56106-193">secret의 값을 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 설정하여 연결 문자열을 완료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="56106-194">비밀 목록</span><span class="sxs-lookup"><span data-stu-id="56106-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-195">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="56106-196">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="56106-197">앞의 예제에서 키 이름의 콜론은 *secrets.json*내의 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56106-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="56106-198">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="56106-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-199">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="56106-200">앱의 *secrets.json* 파일이 `MoviesConnectionString` 키와 연결된 키 값 쌍을 제거하도록 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="56106-201">`dotnet user-secrets list`다음 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="56106-202">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="56106-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-203">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="56106-204">응용 프로그램에 대한 모든 사용자 암호는 *secrets.json* 파일에서 삭제되었습니다 :</span><span class="sxs-lookup"><span data-stu-id="56106-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="56106-205">실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="56106-206">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="56106-206">Additional resources</span></span>

* <span data-ttu-id="56106-207">IIS에서 비밀 관리자에 액세스하는 방법에 대한 자세한 내용은 [이 문제를](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56106-208">[릭 앤더슨,](https://twitter.com/RickAndMSFT) [다니엘 로스,](https://github.com/danroth27) [스콧 애디](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="56106-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="56106-209">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56106-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="56106-210">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발하는 동안 중요한 데이터를 저장하고 검색하는 기술을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="56106-211">암호 또는 기타 중요한 데이터를 소스 코드에 저장하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="56106-212">프로덕션 비밀은 개발 또는 테스트에 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="56106-213">비밀은 앱과 함께 배포해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="56106-214">대신 환경 변수, Azure 키 볼트 등과 같은 제어된 수단을 통해 프로덕션 환경에서 비밀을 사용할 수 있어야 합니다. [Azure Key Vault 구성 공급자를](xref:security/key-vault-configuration)통해 Azure 테스트 및 프로덕션 비밀을 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="56106-215">환경 변수</span><span class="sxs-lookup"><span data-stu-id="56106-215">Environment variables</span></span>

<span data-ttu-id="56106-216">환경 변수는 코드 또는 로컬 구성 파일에 앱 비밀의 저장을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="56106-217">환경 변수는 이전에 지정된 모든 구성 원본에 대한 구성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="56106-218">**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="56106-219">기본 데이터베이스 연결 문자열은 키와 `DefaultConnection`함께 프로젝트의 *appsettings.json* 파일에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="56106-220">기본 연결 문자열은 사용자 모드에서 실행되며 암호가 필요하지 않은 LocalDB용입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="56106-221">앱 배포 중에 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="56106-222">환경 변수는 중요한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="56106-223">환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="56106-224">컴퓨터 또는 프로세스가 손상된 경우 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="56106-225">사용자 기밀의 공개를 방지하기 위한 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="56106-226">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="56106-226">Secret Manager</span></span>

<span data-ttu-id="56106-227">비밀 관리자 도구는 ASP.NET 핵심 프로젝트를 개발하는 동안 중요한 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="56106-228">이 컨텍스트에서 중요한 데이터는 앱 보안 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="56106-229">앱 암호는 프로젝트 트리와 별도의 위치에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="56106-230">앱 비밀은 특정 프로젝트와 연결되거나 여러 프로젝트에서 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="56106-231">앱 비밀이 소스 제어에 체크 인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="56106-232">비밀 관리자 도구는 저장된 암호를 암호화하지 않으며 신뢰할 수 있는 저장소로 취급해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="56106-233">그것은 단지 개발 목적을 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-233">It's for development purposes only.</span></span> <span data-ttu-id="56106-234">키와 값은 사용자 프로필 디렉터리에서 JSON 구성 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="56106-235">시크릿 관리자 도구의 작동 방식</span><span class="sxs-lookup"><span data-stu-id="56106-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="56106-236">비밀 관리자 도구는 값이 저장되는 위치와 방법과 같은 구현 세부 정보를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="56106-237">이러한 구현 세부 정보를 알지 못해도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="56106-238">값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 JSON 구성 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="56106-239">Windows</span><span class="sxs-lookup"><span data-stu-id="56106-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="56106-240">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="56106-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="56106-241">리눅스 / 맥OS</span><span class="sxs-lookup"><span data-stu-id="56106-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="56106-242">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="56106-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="56106-243">앞의 파일 경로에서 `<user_secrets_id>` *.csproj* 파일에 지정된 `UserSecretsId` 값으로 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="56106-244">비밀 관리자 도구로 저장된 데이터의 위치 나 형식에 따라 코드를 작성하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="56106-245">이러한 구현 세부 정보는 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-245">These implementation details may change.</span></span> <span data-ttu-id="56106-246">예를 들어 보안 값은 암호화되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="56106-247">비밀 저장 사용</span><span class="sxs-lookup"><span data-stu-id="56106-247">Enable secret storage</span></span>

<span data-ttu-id="56106-248">비밀 관리자 도구는 사용자 프로필에 저장된 프로젝트별 구성 설정에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="56106-249">사용자 암호를 사용하려면 `UserSecretsId` `PropertyGroup` *.csproj* 파일 내에서 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="56106-250">의 `UserSecretsId` 내부 텍스트는 임의이지만 프로젝트에 고유합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="56106-251">개발자는 일반적으로 에 대한 `UserSecretsId`GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="56106-252">Visual Studio에서 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **사용자 암호 관리를** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="56106-253">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="56106-254">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="56106-254">Set a secret</span></span>

<span data-ttu-id="56106-255">키와 해당 값으로 구성된 앱 암호를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="56106-256">비밀은 프로젝트의 `UserSecretsId` 값과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="56106-257">예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="56106-258">앞의 예제에서 콜론은 `Movies` `ServiceApiKey` 속성이 있는 개체 리터럴을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56106-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="56106-259">비밀 관리자 도구도 다른 디렉토리에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="56106-260">`--project` 옵션을 사용하여 *.csproj* 파일이 있는 파일 시스템 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="56106-261">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="56106-262">비주얼 스튜디오에서 JSON 구조 평탄화</span><span class="sxs-lookup"><span data-stu-id="56106-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="56106-263">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *secrets.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="56106-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="56106-264">*secrets.json의* 내용을 저장할 키 값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56106-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="56106-265">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="56106-266">JSON 구조를 통해 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`수정 후 평평해져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="56106-267">예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"` 실행하면 `Movies` 개체가 리터럴로 축소됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="56106-268">수정된 파일은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="56106-269">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="56106-269">Set multiple secrets</span></span>

<span data-ttu-id="56106-270">`set` JSON을 명령으로 파이핑하여 일괄 처리를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="56106-271">다음 예제에서는 *input.json* 파일의 내용을 `set` 명령에 파이프합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="56106-272">Windows</span><span class="sxs-lookup"><span data-stu-id="56106-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="56106-273">명령 셸을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="56106-274">리눅스 / 맥OS</span><span class="sxs-lookup"><span data-stu-id="56106-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="56106-275">명령 셸을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="56106-276">비밀에 액세스</span><span class="sxs-lookup"><span data-stu-id="56106-276">Access a secret</span></span>

<span data-ttu-id="56106-277">[ASP.NET 핵심 구성 API는](xref:fundamentals/configuration/index) 비밀 관리자 비밀에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="56106-278">프로젝트가 .NET 프레임워크를 대상으로 하는 경우 [Microsoft.Extensions.Configuration.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="56106-279">ASP.NET Core 2.0 이상에서는 프로젝트가 미리 구성된 기본값으로 호스트의 새 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 인스턴스를 초기화하기 위해 호출할 때 사용자 비밀 구성 소스가 개발 모드에 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="56106-280">`CreateDefaultBuilder`있는 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 경우 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>호출 :</span><span class="sxs-lookup"><span data-stu-id="56106-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="56106-281">`CreateDefaultBuilder` 호출되지 않으면 생성자를 호출하여 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 사용자 암호 구성 `Startup` 원본을 명시적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="56106-282">다음 `AddUserSecrets` 예제와 같이 앱이 개발 환경에서 실행되는 경우에만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="56106-283">사용자 암호는 API를 `Configuration` 통해 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="56106-284">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="56106-284">Map secrets to a POCO</span></span>

<span data-ttu-id="56106-285">전체 개체를 POCO(속성이 있는 간단한 .NET 클래스)에 매핑하는 것은 관련 속성을 집계하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-286">선행 비밀을 POCO에 매핑하려면 `Configuration` API의 개체 [그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="56106-287">다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 속성 값에 `ServiceApiKey` 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="56106-288">`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 비밀은 `MovieSettings`다음의 각 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="56106-289">비밀이 있는 문자열 교체</span><span class="sxs-lookup"><span data-stu-id="56106-289">String replacement with secrets</span></span>

<span data-ttu-id="56106-290">암호를 일반 텍스트로 저장하는 것은 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="56106-291">예를 들어 *appsettings.json에* 저장된 데이터베이스 연결 문자열에는 지정된 사용자의 암호가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="56106-292">보다 안전한 방법은 암호를 암호로 저장하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="56106-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="56106-293">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="56106-294">`Password` *appsettings.json의*연결 문자열에서 키-값 쌍을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="56106-295">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="56106-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="56106-296">secret의 값을 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 설정하여 연결 문자열을 완료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="56106-297">비밀 목록</span><span class="sxs-lookup"><span data-stu-id="56106-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-298">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="56106-299">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="56106-300">앞의 예제에서 키 이름의 콜론은 *secrets.json*내의 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56106-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="56106-301">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="56106-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-302">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="56106-303">앱의 *secrets.json* 파일이 `MoviesConnectionString` 키와 연결된 키 값 쌍을 제거하도록 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="56106-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="56106-304">실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="56106-305">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="56106-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="56106-306">*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56106-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="56106-307">응용 프로그램에 대한 모든 사용자 암호는 *secrets.json* 파일에서 삭제되었습니다 :</span><span class="sxs-lookup"><span data-stu-id="56106-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="56106-308">실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="56106-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="56106-309">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="56106-309">Additional resources</span></span>

* <span data-ttu-id="56106-310">IIS에서 비밀 관리자에 액세스하는 방법에 대한 자세한 내용은 [이 문제를](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="56106-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end