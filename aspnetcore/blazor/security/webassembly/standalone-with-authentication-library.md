---
title: 인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: be87257c5f901e9b3d1ba6a8d7c6b811419c433f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402197"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="486d1-102">인증 라이브러리를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="486d1-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="486d1-103">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="486d1-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="486d1-104">‘AAD(Azure Active Directory) 및 AAD B2C(Azure Active Directory B2C)의 경우에는 이 항목의 지침을 따르지 않습니다. 목차 노드 테이블에서 AAD 및 AAD B2C 항목을 참조하세요.’</span><span class="sxs-lookup"><span data-stu-id="486d1-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="486d1-105">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 라이브러리를 사용하는 Blazor WebAssembly 독립 실행형 앱을 만들려면 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="486d1-106">출력 위치를 지정하려면 명령에 경로와 함께 출력 옵션을 포함합니다(예: `-o BlazorSample`). 출력 위치는 프로젝트 폴더가 존재하지 않는 경우 프로젝트 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="486d1-107">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="486d1-108">Visual Studio에서 [Blazor WebAssembly 앱을 만듭니다](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="486d1-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="486d1-109">**사용자 계정 앱 내 저장** 옵션을 사용하여 **인증**을 **개별 사용자 계정**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="486d1-110">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="486d1-110">Authentication package</span></span>

<span data-ttu-id="486d1-111">앱이 개별 사용자 계정을 사용하도록 만들어진 경우 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="486d1-112">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="486d1-113">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="486d1-114">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="486d1-114">Authentication service support</span></span>

<span data-ttu-id="486d1-115">사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="486d1-116">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="486d1-117">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="486d1-117">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="486d1-118">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-118">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="486d1-119">독립 실행형 앱에 대한 인증 지원은 OIDC(Open ID Connect)를 사용하여 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="486d1-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 메서드는 콜백을 받아서 OIDC를 사용하여 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="486d1-121">앱을 구성하는 데 필요한 값은 OIDC 규격 IP에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="486d1-122">앱을 등록할 때 값을 확인하세요. 앱 등록은 보통 온라인 포털에서 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="486d1-123">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="486d1-123">Access token scopes</span></span>

<span data-ttu-id="486d1-124">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="486d1-125">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>의 기본 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="486d1-126">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="486d1-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="486d1-127">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="486d1-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="486d1-128">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="486d1-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="486d1-129">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="486d1-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="486d1-130">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="486d1-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="486d1-131">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="486d1-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="486d1-132">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="486d1-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="486d1-133">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="486d1-133">LoginDisplay component</span></span>

<span data-ttu-id="486d1-134">`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-134">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="486d1-135">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="486d1-135">For authenticated users:</span></span>
  * <span data-ttu-id="486d1-136">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-136">Displays the current username.</span></span>
  * <span data-ttu-id="486d1-137">앱에서 로그아웃하는 단추를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="486d1-138">익명 사용자의 경우, 로그인 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="486d1-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="486d1-139">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="486d1-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="486d1-140">추가 자료</span><span class="sxs-lookup"><span data-stu-id="486d1-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="486d1-141">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="486d1-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
