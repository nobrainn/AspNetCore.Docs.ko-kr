---
<span data-ttu-id="6b4e9-101">제목: ' Blazor Microsoft 계정을 사용 하 여 ASP.NET Core weasemboman 독립 실행형 앱을 보호 합니다. ' 작성자: 설명: monikerRange: ms. 작성자: 밀리초: 사용자 지정: ms. 날짜:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6b4e9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6b4e9-102">'Blazor'</span></span>
- <span data-ttu-id="6b4e9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6b4e9-103">'Identity'</span></span>
- <span data-ttu-id="6b4e9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6b4e9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6b4e9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6b4e9-105">'Razor'</span></span>
- <span data-ttu-id="6b4e9-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="6b4e9-107">BlazorMicrosoft 계정을 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="6b4e9-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="6b4e9-108">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b4e9-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b4e9-109">Blazor인증을 위해 [AAD (Azure Active Directory)를](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용 하는 Microsoft 계정을 사용 하는 weasembom독립형 앱을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="6b4e9-110">AAD 테 넌 트 및 웹 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="6b4e9-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="6b4e9-111">Azure Portal의 **Azure Active Directory**  >  **앱 등록** 영역에서 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6b4e9-112">앱에 대 한 **이름** (예: \*\* Blazor 독립 실행형 AAD Microsoft 계정\*\*)을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="6b4e9-113">**지원 되는 계정 유형**에서 **조직 디렉터리의 계정**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="6b4e9-114">**리디렉션 uri** 드롭다운을 **웹**으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-114">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6b4e9-115">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6b4e9-116">IIS Express의 경우 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-116">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="6b4e9-117">**Permissions**  >  **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6b4e9-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-118">Select **Register**.</span></span>

<span data-ttu-id="6b4e9-119">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="6b4e9-119">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="6b4e9-120">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-120">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6b4e9-121">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-121">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6b4e9-122">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-122">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6b4e9-123">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-123">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6b4e9-124">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-124">Select the **Save** button.</span></span>

<span data-ttu-id="6b4e9-125">앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-125">Create the app.</span></span> <span data-ttu-id="6b4e9-126">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-126">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="6b4e9-127">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="6b4e9-127">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6b4e9-128">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-128">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="6b4e9-129">앱을 만든 후 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-129">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="6b4e9-130">Microsoft 계정를 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-130">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="6b4e9-131">Microsoft Api에 대 한 액세스 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-131">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="6b4e9-132">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-132">For more information, see:</span></span>
  * [<span data-ttu-id="6b4e9-133">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="6b4e9-133">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="6b4e9-134">[빠른 시작: 웹 api를 노출 하도록 응용 프로그램을 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-134">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6b4e9-135">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="6b4e9-135">Authentication package</span></span>

<span data-ttu-id="6b4e9-136">회사 또는 학교 계정 ()을 사용 하도록 앱을 만들 때 `SingleOrg` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="6b4e9-136">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6b4e9-137">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-137">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6b4e9-138">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-138">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="6b4e9-139">`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6b4e9-140">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="6b4e9-140">Authentication service support</span></span>

<span data-ttu-id="6b4e9-141">사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="6b4e9-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6b4e9-142">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6b4e9-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="6b4e9-144">`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6b4e9-145">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-145">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="6b4e9-146">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="6b4e9-147">예제:</span><span class="sxs-lookup"><span data-stu-id="6b4e9-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="6b4e9-148">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="6b4e9-148">Access token scopes</span></span>

<span data-ttu-id="6b4e9-149">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6b4e9-150">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 `MsalProviderOptions` .</span><span class="sxs-lookup"><span data-stu-id="6b4e9-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="6b4e9-151">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="6b4e9-151">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6b4e9-152">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="6b4e9-152">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6b4e9-153">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="6b4e9-153">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="6b4e9-154">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="6b4e9-154">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6b4e9-155">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="6b4e9-155">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6b4e9-156">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6b4e9-156">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6b4e9-157">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6b4e9-157">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6b4e9-158">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6b4e9-158">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6b4e9-159">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6b4e9-159">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6b4e9-160">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6b4e9-160">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="6b4e9-161">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="6b4e9-161">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="6b4e9-162">빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록</span><span class="sxs-lookup"><span data-stu-id="6b4e9-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="6b4e9-163">빠른 시작: 웹 API를 공개하는 애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="6b4e9-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
