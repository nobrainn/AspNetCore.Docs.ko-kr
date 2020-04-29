---
title: ASP.NET Core Blazor 서버 추가 보안 시나리오
author: guardrex
description: 추가 보안 시나리오를 Blazor 위해 서버를 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206349"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="91d89-103">ASP.NET Core Blazor 서버 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="91d89-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="91d89-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="91d89-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="91d89-105">Blazor 서버 앱에 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="91d89-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="91d89-106">Blazor 서버 앱의 Razor 구성 요소 외부에서 사용할 수 있는 토큰은이 섹션에 설명 된 방법으로 구성 요소에 전달 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="91d89-107">전체 `Startup.ConfigureServices` 예제를 비롯 한 샘플 코드는 [서버 쪽 Blazor 응용 프로그램에 토큰 전달](https://github.com/javiercn/blazor-server-aad-sample)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="91d89-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="91d89-108">일반 Razor Pages Blazor 또는 MVC 앱에서와 같이 서버 앱을 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="91d89-109">토큰을 프로 비전 하 여 인증 쿠키에 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="91d89-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-110">For example:</span></span>

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

<span data-ttu-id="91d89-111">액세스 및 새로 고침 토큰을 사용 하 여 초기 앱 상태를 전달 하는 클래스를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="91d89-112">앱 내에서 사용 하 여 [DI (종속성 주입)](xref:blazor/dependency-injection)에서 토큰을 확인할 수 있는 범위 토큰 공급자 서비스를 정의 합니다. **scoped** Blazor</span><span class="sxs-lookup"><span data-stu-id="91d89-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="91d89-113">에서 `Startup.ConfigureServices`다음에 대 한 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="91d89-114">*_Host cshtml* 파일에서 및 인스턴스 `InitialApplicationState` 를 만들고 앱에 매개 변수로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="91d89-115">`App` 구성 요소 (*응용 프로그램 razor*)에서 서비스를 확인 하 고 매개 변수에서 데이터를 사용 하 여 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="91d89-116">보안 API 요청을 만드는 서비스에서 토큰 공급자를 삽입 하 고 API를 호출 하는 토큰을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="91d89-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
