---
<span data-ttu-id="40679-101">제목: ' Blazor Azure Active Directory ' 만든이를 사용 하 여 ASP.NET Core weasemboman 독립 실행형 앱에 보안을 설정 합니다. 설명: 설명: monikerRange: ms</span><span class="sxs-lookup"><span data-stu-id="40679-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="40679-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="40679-102">'Blazor'</span></span>
- <span data-ttu-id="40679-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="40679-103">'Identity'</span></span>
- <span data-ttu-id="40679-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="40679-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="40679-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="40679-105">'Razor'</span></span>
- <span data-ttu-id="40679-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="40679-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="40679-107">BlazorAzure Active Directory를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="40679-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="40679-108">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="40679-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="40679-109">Blazor인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="40679-110">[AAD 테 넌 트 및 웹 응용 프로그램을 만듭니다](/azure/active-directory/develop/v2-overview).</span><span class="sxs-lookup"><span data-stu-id="40679-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="40679-111">Azure Portal의 **Azure Active Directory**  >  **앱 등록** 영역에서 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="40679-112">앱에 대 한 **이름** (예: \*\* Blazor 독립 실행형 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="40679-113">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="40679-114">이 환경에 대해서 **만이 조직 디렉터리에서 계정을** 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="40679-115">**리디렉션 uri** 드롭다운을 **웹**으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="40679-116">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="40679-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="40679-117">IIS Express의 경우 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="40679-118">**Permissions**  >  **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="40679-119">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-119">Select **Register**.</span></span>

<span data-ttu-id="40679-120">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-120">Record the following information:</span></span>

* <span data-ttu-id="40679-121">응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="40679-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="40679-122">디렉터리 ID (테 넌 트 ID) (예: `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="40679-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="40679-123">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="40679-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="40679-124">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="40679-125">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="40679-126">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="40679-127">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-127">Select the **Save** button.</span></span>

<span data-ttu-id="40679-128">앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40679-128">Create the app.</span></span> <span data-ttu-id="40679-129">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="40679-130">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="40679-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="40679-131">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40679-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="40679-132">앱을 만든 후 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="40679-133">AAD 사용자 계정을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="40679-134">Microsoft Api에 대 한 액세스 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="40679-135">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40679-135">For more information, see:</span></span>
  * [<span data-ttu-id="40679-136">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="40679-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="40679-137">[빠른 시작: 웹 api를 노출 하도록 응용 프로그램을 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="40679-138">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="40679-138">Authentication package</span></span>

<span data-ttu-id="40679-139">회사 또는 학교 계정 ()을 사용 하도록 앱을 만들 때 `SingleOrg` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="40679-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="40679-140">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="40679-141">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="40679-142">`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="40679-143">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="40679-143">Authentication service support</span></span>

<span data-ttu-id="40679-144">사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="40679-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="40679-145">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="40679-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="40679-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="40679-147">`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="40679-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="40679-148">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="40679-149">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40679-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="40679-150">예제:</span><span class="sxs-lookup"><span data-stu-id="40679-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="40679-151">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="40679-151">Access token scopes</span></span>

<span data-ttu-id="40679-152">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40679-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="40679-153">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 `MsalProviderOptions` .</span><span class="sxs-lookup"><span data-stu-id="40679-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="40679-154">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="40679-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="40679-155">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="40679-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="40679-156">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="40679-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="40679-157">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="40679-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="40679-158">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="40679-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="40679-159">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="40679-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="40679-160">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="40679-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="40679-161">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="40679-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="40679-162">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="40679-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="40679-163">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="40679-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="40679-164">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="40679-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="40679-165">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="40679-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
