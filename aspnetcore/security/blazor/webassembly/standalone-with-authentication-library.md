---
<span data-ttu-id="97197-101">제목: ' Blazor 인증 라이브러리를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱을 보호 합니다. ' 작성자: 설명: monikerRange: ms.:</span><span class="sxs-lookup"><span data-stu-id="97197-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97197-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97197-102">'Blazor'</span></span>
- <span data-ttu-id="97197-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97197-103">'Identity'</span></span>
- <span data-ttu-id="97197-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97197-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="97197-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97197-105">'Razor'</span></span>
- <span data-ttu-id="97197-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="97197-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="97197-107">Blazor인증 라이브러리를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="97197-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="97197-108">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="97197-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="97197-109">*AAD (Azure Active Directory) 및 Azure Active Directory B2C (AAD B2C)의 경우이 항목의 지침을 따르세요. 이 목차 노드의 AAD 및 AAD B2C 항목을 참조 하세요.*</span><span class="sxs-lookup"><span data-stu-id="97197-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="97197-110">Blazor [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 라이브러리를 사용 하는 weasembom를 사용 하는 독립 실행형 앱을 만들려면 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-110">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="97197-111">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="97197-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="97197-112">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="97197-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="97197-113">Visual Studio에서 [ Blazor weasembomapp을 만듭니다](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="97197-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="97197-114">**사용자 계정을 앱에 저장** 옵션을 사용 하 여 **개별 사용자 계정** 에 대 한 **인증** 을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="97197-115">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="97197-115">Authentication package</span></span>

<span data-ttu-id="97197-116">개별 사용자 계정을 사용 하도록 앱을 만들 때 앱은 앱의 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="97197-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="97197-117">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="97197-118">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="97197-119">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="97197-119">Authentication service support</span></span>

<span data-ttu-id="97197-120">사용자 인증에 대 한 지원은 AspNetCore 패키지에서 제공 하는 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 확장 메서드를 사용 하 [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="97197-120">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="97197-121">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="97197-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="97197-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="97197-123">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="97197-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="97197-124">독립형 앱에 대 한 인증 지원은 OIDC (Open ID Connect)를 사용 하 여 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="97197-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="97197-125"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>메서드는 OIDC를 사용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-125">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="97197-126">앱을 구성 하는 데 필요한 값은 OIDC 규격 IP에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="97197-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="97197-127">앱을 등록 하면 일반적으로 온라인 포털에서 발생 하는 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="97197-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="97197-128">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="97197-128">Access token scopes</span></span>

<span data-ttu-id="97197-129">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="97197-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="97197-130">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 토큰 범위에 범위를 추가 합니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="97197-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="97197-131">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="97197-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="97197-132">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="97197-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="97197-133">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="97197-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="97197-134">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="97197-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="97197-135">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="97197-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="97197-136">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="97197-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="97197-137">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="97197-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="97197-138">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="97197-138">LoginDisplay component</span></span>

<span data-ttu-id="97197-139">구성 `LoginDisplay` 요소 (*Shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*shared/mainlayout. razor*)에서 렌더링 되 고 다음 동작을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="97197-140">인증 된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="97197-140">For authenticated users:</span></span>
  * <span data-ttu-id="97197-141">현재 사용자 이름을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-141">Displays the current username.</span></span>
  * <span data-ttu-id="97197-142">앱에서 로그 아웃할 수 있는 단추를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="97197-143">익명 사용자의 경우은 로그인 할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="97197-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="97197-144">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="97197-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="97197-145">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="97197-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="97197-146">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="97197-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
