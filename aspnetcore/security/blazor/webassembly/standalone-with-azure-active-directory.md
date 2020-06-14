---
title: BlazorAzure Active Directory를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 6d8db8f546e21ccce27cca06b4112ebc52653782
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724382"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="fa986-102">BlazorAzure Active Directory를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="fa986-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="fa986-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fa986-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fa986-104">Blazor인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="fa986-105">[AAD 테 넌 트 및 웹 응용 프로그램을 만듭니다](/azure/active-directory/develop/v2-overview).</span><span class="sxs-lookup"><span data-stu-id="fa986-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="fa986-106">Azure Portal의 **Azure Active Directory**  >  **앱 등록** 영역에서 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fa986-107">앱에 대 한 **이름** (예: \*\* Blazor 독립 실행형 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="fa986-108">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="fa986-109">이 환경에 대해서 **만이 조직 디렉터리에서 계정을** 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="fa986-110">**리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="fa986-111">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="fa986-112">앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="fa986-113">IIS Express의 경우 앱에 대해 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="fa986-114">이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="fa986-115">사용자 IIS Express 리디렉션 URI를 업데이트 하는 것을 알리기 위해이 항목의 뒷부분에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="fa986-116">**권한**  >  **부여 관리자 동의에 openid connect 및 offline_access 권한 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fa986-117">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-117">Select **Register**.</span></span>

<span data-ttu-id="fa986-118">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-118">Record the following information:</span></span>

* <span data-ttu-id="fa986-119">응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="fa986-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fa986-120">디렉터리 ID (테 넌 트 ID) (예: `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="fa986-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="fa986-121">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="fa986-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fa986-122">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fa986-123">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fa986-124">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fa986-125">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-125">Select the **Save** button.</span></span>

<span data-ttu-id="fa986-126">앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-126">Create the app.</span></span> <span data-ttu-id="fa986-127">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fa986-128">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="fa986-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fa986-129">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="fa986-130">Azure Portal에서 앱의 **인증**  >  **플랫폼 구성**  >  **웹**  >  **리디렉션 URI** 는 기본 설정으로 kestrel 서버에서 실행 되는 앱에 대 한 포트 5001에 대해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="fa986-131">앱이 임의의 IIS Express 포트에서 실행 되는 경우 앱에 대 한 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="fa986-132">이전에 앱의 알려진 포트를 사용 하 여 포트를 구성 하지 않은 경우 Azure Portal에서 앱 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="fa986-133">앱을 만든 후 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="fa986-134">AAD 사용자 계정을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="fa986-135">Microsoft Api에 대 한 액세스 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="fa986-136">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fa986-136">For more information, see:</span></span>
  * [<span data-ttu-id="fa986-137">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="fa986-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="fa986-138">[빠른 시작: 웹 api를 노출 하도록 응용 프로그램을 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fa986-139">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="fa986-139">Authentication package</span></span>

<span data-ttu-id="fa986-140">회사 또는 학교 계정 ()을 사용 하도록 앱을 만들 때 `SingleOrg` 앱은 [Microsoft 인증 라이브러리 (Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) )에 대 한 패키지 참조를 자동으로 받습니다 (microsoft 인증 라이브러리 ([Microsoft. Authentication. Webassembly](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="fa986-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="fa986-141">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fa986-142">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="fa986-143">AspNetCore [패키지는 응용](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 프로그램에 비 전이적으로 인증 패키지를 추가 하 여 전이적으로 추가 합니다 ( [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ).</span><span class="sxs-lookup"><span data-stu-id="fa986-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fa986-144">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="fa986-144">Authentication service support</span></span>

<span data-ttu-id="fa986-145">사용자 인증 지원은 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="fa986-146">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fa986-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa986-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="fa986-148"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fa986-149">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="fa986-150">구성은 파일 *의 wwwroot/appsettings.js* 에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="fa986-151">예:</span><span class="sxs-lookup"><span data-stu-id="fa986-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="fa986-152">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="fa986-152">Access token scopes</span></span>

<span data-ttu-id="fa986-153">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fa986-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fa986-154">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="fa986-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="fa986-155">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="fa986-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="fa986-156">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="fa986-156">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="fa986-157">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="fa986-157">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="fa986-158">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="fa986-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fa986-159">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="fa986-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fa986-160">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fa986-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fa986-161">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fa986-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fa986-162">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fa986-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fa986-163">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fa986-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fa986-164">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fa986-164">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="fa986-165">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="fa986-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="fa986-166">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="fa986-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
