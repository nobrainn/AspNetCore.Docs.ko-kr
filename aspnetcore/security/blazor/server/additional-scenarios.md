---
title: ASP.NET Core Blazor 서버 추가 보안 시나리오
author: guardrex
description: 추가 보안 시나리오를 위해 서버를 구성 하는 방법을 알아봅니다 Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 159d418a78caa3954294ad0a1067654d895147f7
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679672"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="c9936-103">ASP.NET Core Blazor 서버 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="c9936-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="c9936-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="c9936-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="c9936-105">서버 앱에 토큰 전달 Blazor</span><span class="sxs-lookup"><span data-stu-id="c9936-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="c9936-106">서버 앱의 구성 요소 외부에서 사용할 수 있는 토큰은 Razor Blazor 이 섹션에 설명 된 방법으로 구성 요소에 전달 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="c9936-107">전체 예제를 비롯 한 샘플 코드는 `Startup.ConfigureServices` [서버 쪽 Blazor 응용 프로그램에 토큰 전달](https://github.com/javiercn/blazor-server-aad-sample)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c9936-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="c9936-108">Blazor일반 페이지 또는 MVC 앱에서와 같이 서버 앱을 인증 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="c9936-109">토큰을 프로 비전 하 여 인증 쿠키에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="c9936-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="c9936-111">액세스 및 새로 고침 토큰을 사용 하 여 초기 앱 상태를 전달 하는 클래스를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="c9936-112">앱 내 **scoped** 에서 사용 하 여 Blazor [DI (종속성 주입)](xref:blazor/dependency-injection)에서 토큰을 확인할 수 있는 범위 토큰 공급자 서비스를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="c9936-113">에서 `Startup.ConfigureServices` 다음에 대 한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="c9936-114">*_Host cshtml* 파일에서 및 인스턴스를 만들고 `InitialApplicationState` 앱에 매개 변수로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="c9936-115">`App`구성 요소 (*응용 프로그램 razor*)에서 서비스를 확인 하 고 매개 변수에서 데이터를 사용 하 여 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="c9936-116">보안 API 요청을 만드는 서비스에서 토큰 공급자를 삽입 하 고 API를 호출 하는 토큰을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="c9936-117">인증 체계 설정</span><span class="sxs-lookup"><span data-stu-id="c9936-117">Set the authentication scheme</span></span>

<span data-ttu-id="c9936-118">둘 이상의 인증 미들웨어를 사용 하는 앱의 경우 둘 이상의 인증 체계를 사용 하는 경우 Blazor 의 끝점 구성에서를 사용 하는 스키마를 명시적으로 설정할 수 있습니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="c9936-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="c9936-119">다음 예에서는 Azure Active Directory 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="c9936-120">OIDC (Open ID Connect) v2.0 끝점 사용</span><span class="sxs-lookup"><span data-stu-id="c9936-120">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="c9936-121">인증 라이브러리 및 Blazor 템플릿은 OIDC (OPEN ID Connect) v1.0 끝점을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-121">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="c9936-122">V2.0 끝점을 사용 하려면에서 옵션을 구성 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> .</span><span class="sxs-lookup"><span data-stu-id="c9936-122">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="c9936-123">또는 앱 설정 (*appsettings.json*) 파일에서 설정을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-123">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="c9936-124">권한에 대 한 세그먼트의 추적가 비 AAD 공급자와 같은 앱의 OIDC 공급자에 적합 하지 않은 경우 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-124">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="c9936-125"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>또는 앱 설정 파일에서 키를 사용 하 여 속성을 설정 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> .</span><span class="sxs-lookup"><span data-stu-id="c9936-125">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="c9936-126">코드 변경 내용</span><span class="sxs-lookup"><span data-stu-id="c9936-126">Code changes</span></span>

* <span data-ttu-id="c9936-127">V2.0 끝점에 대 한 ID 토큰의 클레임 목록이 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-127">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="c9936-128">자세한 내용은 [Microsoft identity platform (v2.0)로 업데이트 하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c9936-128">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="c9936-129">Azure 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c9936-129">in the Azure documentation.</span></span>
* <span data-ttu-id="c9936-130">V2.0 끝점에 대 한 범위 Uri에 리소스가 지정 되므로 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 에서 속성 설정을 제거 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> .</span><span class="sxs-lookup"><span data-stu-id="c9936-130">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="c9936-131">OIDC 공급자 앱 등록 설명에서 사용할 앱 ID URI를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9936-131">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
