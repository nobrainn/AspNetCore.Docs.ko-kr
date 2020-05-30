---
<span data-ttu-id="10b0e-101">제목: ASP.NET Core ' Blazor Azure Active Directory B2C ' author: guardrex description: monikerRange: ' >= aspnetcore-3.1 ' ms author: riande. custom: mvc ms. date: 05/19/2020 no loc:</span><span class="sxs-lookup"><span data-stu-id="10b0e-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: guardrex description: monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:</span></span>
- <span data-ttu-id="10b0e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="10b0e-102">'Blazor'</span></span>
- <span data-ttu-id="10b0e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="10b0e-103">'Identity'</span></span>
- <span data-ttu-id="10b0e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="10b0e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="10b0e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="10b0e-105">'Razor'</span></span>
- <span data-ttu-id="10b0e-106">' SignalR ' uid: security/blazor/weasembambb2c</span><span class="sxs-lookup"><span data-stu-id="10b0e-106">'SignalR' uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c</span></span>

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="10b0e-107">BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="10b0e-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="10b0e-108">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10b0e-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="10b0e-109">Blazor인증을 위해 [AAD (AZURE ACTIVE DIRECTORY) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="10b0e-110">다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="10b0e-111">AAD B2C 테 넌 트 만들기</span><span class="sxs-lookup"><span data-stu-id="10b0e-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="10b0e-112">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-112">Record the following information:</span></span>

* <span data-ttu-id="10b0e-113">AAD B2C 인스턴스입니다 (예: `https://contoso.b2clogin.com/` 후행 슬래시를 포함 하는).</span><span class="sxs-lookup"><span data-stu-id="10b0e-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="10b0e-114">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="10b0e-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="10b0e-115">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 *클라이언트 앱* 에 대 한 AAD 앱을 등록 하 고 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="10b0e-116">**Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="10b0e-117">앱에 대 한 **이름** (예: \*\* Blazor 독립 실행형 AAD B2C\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="10b0e-118">**지원 되는 계정 유형**에 대해 다중 테 넌 트 옵션 ( **모든 조직 디렉터리 또는 모든 Id 공급자의 계정)을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="10b0e-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="10b0e-119">**리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-119">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="10b0e-120">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="10b0e-121">앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-121">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="10b0e-122">IIS Express의 경우 앱에 대해 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-122">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="10b0e-123">이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-123">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="10b0e-124">사용자 IIS Express 리디렉션 URI를 업데이트 하는 것을 알리기 위해이 항목의 뒷부분에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-124">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="10b0e-125">**Permissions**  >  **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-125">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="10b0e-126">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-126">Select **Register**.</span></span>

<span data-ttu-id="10b0e-127">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="10b0e-127">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="10b0e-128">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="10b0e-128">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="10b0e-129">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="10b0e-130">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-130">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="10b0e-131">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="10b0e-132">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-132">Select the **Save** button.</span></span>

<span data-ttu-id="10b0e-133">**홈**  >  **Azure AD B2C**  >  **사용자 흐름**:</span><span class="sxs-lookup"><span data-stu-id="10b0e-133">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="10b0e-134">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="10b0e-134">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="10b0e-135">최소한 **응용 프로그램 클레임**  >  **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-135">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="10b0e-136">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="10b0e-136">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="10b0e-137">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-137">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="10b0e-138">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="10b0e-138">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="10b0e-139">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-139">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="10b0e-140">Azure Portal에서 앱의 **인증**  >  **플랫폼 구성**  >  **웹**  >  **리디렉션 URI** 는 기본 설정으로 kestrel 서버에서 실행 되는 앱에 대 한 포트 5001에 대해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-140">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="10b0e-141">앱이 임의의 IIS Express 포트에서 실행 되는 경우 앱에 대 한 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-141">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="10b0e-142">이전에 앱의 알려진 포트를 사용 하 여 포트를 구성 하지 않은 경우 Azure Portal에서 앱 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-142">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="10b0e-143">앱을 만든 후 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-143">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="10b0e-144">AAD 사용자 계정을 사용 하 여 앱에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-144">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="10b0e-145">Microsoft Api에 대 한 액세스 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-145">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="10b0e-146">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="10b0e-146">For more information, see:</span></span>
  * [<span data-ttu-id="10b0e-147">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="10b0e-147">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="10b0e-148">[빠른 시작: 웹 api를 노출 하도록 응용 프로그램을 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-148">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="10b0e-149">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="10b0e-149">Authentication package</span></span>

<span data-ttu-id="10b0e-150">개별 B2C 계정 ()을 사용 하도록 앱을 만들면 `IndividualB2C` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)받습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-150">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="10b0e-151">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-151">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="10b0e-152">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-152">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="10b0e-153">AspNetCore [패키지는 응용](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 프로그램에 비 전이적으로 인증 패키지를 추가 하 여 전이적으로 추가 합니다 ( [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ).</span><span class="sxs-lookup"><span data-stu-id="10b0e-153">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="10b0e-154">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="10b0e-154">Authentication service support</span></span>

<span data-ttu-id="10b0e-155">사용자 인증 지원은 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-155">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="10b0e-156">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-156">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="10b0e-157">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="10b0e-157">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="10b0e-158"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-158">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="10b0e-159">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-159">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="10b0e-160">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-160">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="10b0e-161">예:</span><span class="sxs-lookup"><span data-stu-id="10b0e-161">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="10b0e-162">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="10b0e-162">Access token scopes</span></span>

<span data-ttu-id="10b0e-163">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10b0e-163">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="10b0e-164">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="10b0e-164">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="10b0e-165">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="10b0e-165">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="10b0e-166">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="10b0e-166">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="10b0e-167">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="10b0e-167">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="10b0e-168">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="10b0e-168">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="10b0e-169">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="10b0e-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="10b0e-170">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="10b0e-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="10b0e-171">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="10b0e-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="10b0e-172">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="10b0e-172">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="10b0e-173">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="10b0e-173">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="10b0e-174">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="10b0e-174">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="10b0e-175">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="10b0e-175">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="10b0e-176">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="10b0e-176">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="10b0e-177">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="10b0e-177">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
