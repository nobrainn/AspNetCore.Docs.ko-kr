---
title: 추가 Blazor 보안 시나리오 ASP.NET Core
author: guardrex
description: 추가 보안 시나리오에 Blazor 대해 weasembmbom를 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 093498c3e0d42430c66c66a0998bcc44f62d1e0d
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206153"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="8eb3f-103">ASP.NET Core Blazor Weasembmbambambambamba 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="8eb3f-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="8eb3f-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="8eb3f-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="8eb3f-105">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="8eb3f-105">Request additional access tokens</span></span>

<span data-ttu-id="8eb3f-106">대부분의 앱은 사용 하는 보호 된 리소스와 상호 작용 하는 데 액세스 토큰만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-106">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="8eb3f-107">일부 시나리오에서는 둘 이상의 리소스와 상호 작용 하기 위해 앱에 둘 이상의 토큰이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-107">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="8eb3f-108">다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내기 위해 AAD (추가 Azure Active Directory) Microsoft Graph API 범위가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-108">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="8eb3f-109">Azure AAD 포털에서 Microsoft Graph API 사용 권한을 추가한 후 클라이언트 앱 (`Program.Main`, *Program.cs*)에서 추가 범위가 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-109">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="8eb3f-110">메서드 `IAccessTokenProvider.RequestToken` 는 다음 예제와 같이 응용 프로그램에서 지정 된 범위 집합을 사용 하 여 액세스 토큰을 프로 비전 할 수 있도록 하는 오버 로드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-110">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="8eb3f-111">`TryGetToken`전용인</span><span class="sxs-lookup"><span data-stu-id="8eb3f-111">`TryGetToken` returns:</span></span>

* <span data-ttu-id="8eb3f-112">`true``token` 사용할를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-112">`true` with the `token` for use.</span></span>
* <span data-ttu-id="8eb3f-113">`false`토큰이 검색 되지 않으면입니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-113">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="8eb3f-114">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="8eb3f-114">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="8eb3f-115">서비스 `AuthorizationMessageHandler` 를와 함께 `HttpClient` 사용 하 여 액세스 토큰을 보내는 요청에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-115">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="8eb3f-116">토큰은 기존 `IAccessTokenProvider` 서비스를 사용 하 여 획득 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-116">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="8eb3f-117">토큰을 가져올 수 없는 경우 `AccessTokenNotAvailableException` 이 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-117">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="8eb3f-118">`AccessTokenNotAvailableException`에는 `Redirect` 사용자가 id 공급자로 이동 하 여 새 토큰을 획득 하는 데 사용할 수 있는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-118">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="8eb3f-119">는 `AuthorizationMessageHandler` `ConfigureHandler` 메서드를 사용 하 여 권한 있는 url, 범위 및 반환 URL을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-119">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="8eb3f-120">다음 예제 `AuthorizationMessageHandler` 에서는 (*Program.cs*) `HttpClient` 에서 `Program.Main` 를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-120">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="8eb3f-121">편의상 앱 기준 주소 `BaseAddressAuthorizationMessageHandler` 를 권한 있는 URL로 미리 구성 된가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-121">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="8eb3f-122">이제 인증 사용 Blazor Weasembomtemplate 템플릿이 [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) 를 사용 하 여를 설정 `HttpClient` 합니다 `BaseAddressAuthorizationMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-122">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="8eb3f-123">앞의 예제 `CreateClient` 에서 클라이언트를 만든 경우 서버 프로젝트에 요청 `HttpClient` 을 수행할 때 액세스 토큰이 포함 된 인스턴스가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-123">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="8eb3f-124">그런 다음 `HttpClient` 구성 된를 사용 하 여 간단한 `try-catch` 패턴을 통해 권한 있는 요청을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-124">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="8eb3f-125">다음 `FetchData` 구성 요소는 날씨 예보 데이터를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-125">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="8eb3f-126">또는 단일 클래스 내에서 모든 HTTP 및 토큰 획득 문제를 처리 하는 형식화 된 클라이언트를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-126">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="8eb3f-127">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="8eb3f-127">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="8eb3f-128">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8eb3f-128">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="8eb3f-129">*Fetchdata. razor*:</span><span class="sxs-lookup"><span data-stu-id="8eb3f-129">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="8eb3f-130">토큰 요청 오류 처리</span><span class="sxs-lookup"><span data-stu-id="8eb3f-130">Handle token request errors</span></span>

<span data-ttu-id="8eb3f-131">SPA (단일 페이지 응용 프로그램)가 OIDC (Open ID Connect)를 사용 하 여 사용자를 인증 하는 경우 인증 상태는 해당 자격 증명을 제공 하는 사용자의 결과로 설정 된 세션 쿠키의 형태로 SPA 및 IP (Id 공급자) 내에서 로컬로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-131">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="8eb3f-132">일반적으로 사용자에 대 한 IP가 내보내는 토큰은 짧은 시간 동안 일반적으로 1 시간 동안 유효 하므로 클라이언트 앱이 정기적으로 새 토큰을 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-132">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="8eb3f-133">그렇지 않으면 부여 된 토큰이 만료 된 후 사용자가 로그 아웃 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-133">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="8eb3f-134">대부분의 경우 OIDC 클라이언트는 사용자가 IP 내에 유지 되는 인증 상태 또는 "세션"으로 인해 다시 인증 하도록 요구 하지 않고 새 토큰을 프로 비전 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-134">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="8eb3f-135">사용자가 사용자 개입 없이 토큰을 가져올 수 없는 경우도 있습니다. 예를 들어 사용자가 IP에서 명시적으로 로그 아웃 하는 이유가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-135">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="8eb3f-136">이 시나리오는 사용자가 방문 `https://login.microsoftonline.com` 하 여 로그 아웃 하는 경우에 발생 합니다. 이러한 시나리오에서 앱은 사용자가 로그 아웃 한 즉시 인식 하지 못합니다. 클라이언트에서 보유 하는 모든 토큰은 더 이상 유효 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-136">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="8eb3f-137">또한 클라이언트는 현재 토큰이 만료 된 후 사용자 상호 작용 없이 새 토큰을 프로 비전 할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-137">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="8eb3f-138">이러한 시나리오는 토큰 기반 인증에 국한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-138">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="8eb3f-139">이러한 특성은 SPAs 특성의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-139">They are part of the nature of SPAs.</span></span> <span data-ttu-id="8eb3f-140">쿠키를 사용 하는 SPA는 인증 쿠키를 제거 하는 경우에도 서버 API를 호출 하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-140">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="8eb3f-141">앱에서 보호 된 리소스에 대 한 API 호출을 수행 하는 경우 다음 사항을 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-141">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="8eb3f-142">새 액세스 토큰을 프로 비전 하 여 API를 호출 하려면 사용자가 다시 인증 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-142">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="8eb3f-143">클라이언트에 유효한 토큰이 있는 경우에도 토큰을 사용자가 해지 했기 때문에 서버에 대 한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-143">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="8eb3f-144">앱에서 토큰을 요청 하는 경우 다음과 같은 두 가지 결과를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-144">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="8eb3f-145">요청이 성공 하 고 앱에 유효한 토큰이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-145">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="8eb3f-146">요청이 실패 하 고 앱이 사용자를 다시 인증 하 여 새 토큰을 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-146">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="8eb3f-147">토큰 요청이 실패 하는 경우 리디렉션을 수행 하기 전에 현재 상태를 저장할 것인지 여부를 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-147">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="8eb3f-148">복잡성 수준이 증가 하는 방법에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-148">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="8eb3f-149">세션 저장소에 현재 페이지 상태를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-149">Store the current page state in session storage.</span></span> <span data-ttu-id="8eb3f-150">`OnInitializeAsync`에서 계속 하기 전에 상태를 복원할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-150">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="8eb3f-151">쿼리 문자열 매개 변수를 추가 하 고이 매개 변수를 사용 하 여 이전에 저장 된 상태를 다시 하이드레이션 하며 나중 하는 데 필요한 것으로 앱에 신호를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-151">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="8eb3f-152">다른 항목과 충돌을 발생 시 키 지 않고 세션 저장소에 데이터를 저장 하는 고유 식별자를 사용 하 여 쿼리 문자열 매개 변수를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-152">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="8eb3f-153">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-153">The following example shows how to:</span></span>

* <span data-ttu-id="8eb3f-154">로그인 페이지로 리디렉션하기 전에 상태를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-154">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="8eb3f-155">쿼리 문자열 매개 변수를 사용 하 여 인증 이후 이전 상태를 복구 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-155">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="8eb3f-156">인증 작업 전에 앱 상태 저장</span><span class="sxs-lookup"><span data-stu-id="8eb3f-156">Save app state before an authentication operation</span></span>

<span data-ttu-id="8eb3f-157">인증 작업을 수행 하는 동안 브라우저가 IP로 리디렉션되도록 응용 프로그램 상태를 저장 하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-157">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="8eb3f-158">상태 컨테이너와 같은 항목을 사용 하 고 인증에 성공 하 고 나면 상태를 복원 하려는 경우를 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-158">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="8eb3f-159">사용자 지정 인증 상태 개체를 사용 하 여 앱 특정 상태 또는 참조를 보존 하 고 인증 작업이 성공적으로 완료 되 면 해당 상태를 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-159">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="8eb3f-160">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="8eb3f-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="8eb3f-161">앱 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8eb3f-161">Customize app routes</span></span>

<span data-ttu-id="8eb3f-162">기본적으로 라이브러리는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 다음 표에 나와 있는 경로를 사용 하 여 서로 다른 인증 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-162">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="8eb3f-163">라우팅</span><span class="sxs-lookup"><span data-stu-id="8eb3f-163">Route</span></span>                            | <span data-ttu-id="8eb3f-164">목적</span><span class="sxs-lookup"><span data-stu-id="8eb3f-164">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="8eb3f-165">로그인 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-165">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="8eb3f-166">로그인 작업의 결과를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-166">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="8eb3f-167">어떤 이유로 인해 로그인 작업이 실패 하는 경우 오류 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-167">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="8eb3f-168">로그 아웃 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-168">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="8eb3f-169">로그 아웃 작업의 결과를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-169">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="8eb3f-170">어떤 이유로 인해 로그 아웃 작업이 실패 하는 경우 오류 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-170">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="8eb3f-171">사용자가 성공적으로 로그 아웃 했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-171">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="8eb3f-172">사용자 프로필을 편집 하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-172">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="8eb3f-173">새 사용자를 등록 하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-173">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="8eb3f-174">위의 표에 표시 된 경로는를 통해 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-174">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="8eb3f-175">사용자 지정 경로를 제공 하는 옵션을 설정 하는 경우 앱에 각 경로를 처리 하는 경로가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-175">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="8eb3f-176">다음 예제에서 모든 경로에는 접두사가 붙습니다 `/security`.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-176">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="8eb3f-177">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="8eb3f-177">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="8eb3f-178">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="8eb3f-178">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="8eb3f-179">요구 사항이 완전히 다른 경로에 대해를 호출 하는 경우 앞에서 설명한 대로 경로를 `RemoteAuthenticatorView` 설정 하 고 명시적인 동작 매개 변수를 사용 하 여을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-179">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="8eb3f-180">이렇게 선택 하면 UI를 다른 페이지로 나눌 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-180">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="8eb3f-181">인증 사용자 인터페이스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8eb3f-181">Customize the authentication user interface</span></span>

<span data-ttu-id="8eb3f-182">`RemoteAuthenticatorView`에는 각 인증 상태에 대 한 기본 UI 조각 집합이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-182">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="8eb3f-183">사용자 지정 `RenderFragment`를 전달 하 여 각 상태를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-183">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="8eb3f-184">초기 로그인 프로세스 중에 표시 되는 텍스트를 사용자 지정 하려면에서 `RemoteAuthenticatorView` 다음과 같이 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-184">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="8eb3f-185">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="8eb3f-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="8eb3f-186">에 `RemoteAuthenticatorView` 는 다음 표에 표시 된 인증 경로 별로 사용할 수 있는 하나의 조각이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-186">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="8eb3f-187">라우팅</span><span class="sxs-lookup"><span data-stu-id="8eb3f-187">Route</span></span>                            | <span data-ttu-id="8eb3f-188">조각</span><span class="sxs-lookup"><span data-stu-id="8eb3f-188">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="8eb3f-189">사용자 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8eb3f-189">Customize the user</span></span>

<span data-ttu-id="8eb3f-190">앱에 바인딩된 사용자를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-190">Users bound to the app can be customized.</span></span> <span data-ttu-id="8eb3f-191">다음 예에서는 인증 된 모든 사용자가 각 사용자 `amr` 의 인증 방법에 대 한 클레임을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-191">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="8eb3f-192">`RemoteUserAccount` 클래스를 확장 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-192">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="8eb3f-193">다음을 확장 `AccountClaimsPrincipalFactory<TAccount>`하는 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-193">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="8eb3f-194">다음을 사용 하도록 서비스 `CustomAccountFactory`를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-194">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="8eb3f-195">인증을 사용한 미리 렌더링 지원</span><span class="sxs-lookup"><span data-stu-id="8eb3f-195">Support prerendering with authentication</span></span>

<span data-ttu-id="8eb3f-196">호스트된 Blazor WebAssembly 앱 토픽 중 하나의 지침을 따른 후에는 다음 지침에 따라 다음과 같은 동작을 수행하는 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-196">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="8eb3f-197">인증이 필요하지 않은 경로를 미리 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-197">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="8eb3f-198">인증이 필요한 경로를 미리 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-198">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="8eb3f-199">클라이언트 앱의 `Program` 클래스(*Program.cs*)에서 공통 서비스 등록을 별도의 메서드(예: `ConfigureCommonServices`)로 팩터링합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-199">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="8eb3f-200">서버 앱의 `Startup.ConfigureServices`에서 다음과 같은 추가 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-200">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="8eb3f-201">서버 앱의 `Startup.Configure` 메서드에서 `endpoints.MapFallbackToFile("index.html")`을 `endpoints.MapFallbackToPage("/_Host")`로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-201">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="8eb3f-202">서버 앱에서 *Pages* 폴더가 없으면 이 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-202">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="8eb3f-203">서버 앱의 *Pages* 폴더 안에 *_Host.cshtml* 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-203">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="8eb3f-204">클라이언트 앱의 *wwwroot/index.html* 파일 콘텐츠를 *Pages/_Host.cshtml* 파일에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-204">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="8eb3f-205">다음과 같이 파일 콘텐츠를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-205">Update the file's contents:</span></span>

* <span data-ttu-id="8eb3f-206">`@page "_Host"`를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-206">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="8eb3f-207">`<app>Loading...</app>` 태그를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-207">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="8eb3f-208">호스트된 앱 및 타사 로그인 공급자에 대한 옵션</span><span class="sxs-lookup"><span data-stu-id="8eb3f-208">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="8eb3f-209">타사 공급자를 사용하여 호스트된 Blazor WebAssembly 앱을 인증하고 권한을 부여하는 경우 사용자를 인증하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-209">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="8eb3f-210">시나리오에 따라 선택하는 옵션이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-210">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="8eb3f-211">자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-211">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="8eb3f-212">보호된 타사 API만 호출하도록 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="8eb3f-212">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="8eb3f-213">클라이언트 쪽 OAuth 흐름을 사용하여 사용자를 타사 API 공급자에 대해 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-213">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="8eb3f-214">이 시나리오에서는</span><span class="sxs-lookup"><span data-stu-id="8eb3f-214">In this scenario:</span></span>

* <span data-ttu-id="8eb3f-215">앱을 호스트하는 서버에서 역할을 재생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-215">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="8eb3f-216">서버의 API는 보호할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-216">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="8eb3f-217">앱은 보호된 타사 API만 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-217">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="8eb3f-218">타사 공급자를 사용하여 사용자를 인증하고 호스트 서버 및 타사에서 보호된 API 호출</span><span class="sxs-lookup"><span data-stu-id="8eb3f-218">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="8eb3f-219">타사 로그인 공급자를 사용하여 ID를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-219">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="8eb3f-220">타사 API 액세스에 필요한 토큰을 가져와 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-220">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="8eb3f-221">사용자가 로그인하면 ID는 인증 프로세스의 일부로 액세스 및 새로 고침 토큰을 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-221">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="8eb3f-222">이때 타사 API에 대한 API 호출을 수행하는 데 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-222">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="8eb3f-223">서버 액세스 토큰을 사용하여 타사 액세스 토큰 검색</span><span class="sxs-lookup"><span data-stu-id="8eb3f-223">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="8eb3f-224">서버에서 생성된 액세스 토큰을 사용하여 서버 API 엔드포인트에서 타사 액세스 토큰을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-224">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="8eb3f-225">여기에서 타사 액세스 토큰을 사용하여 클라이언트의 ID에서 직접 타사 API 리소스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-225">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="8eb3f-226">이 방법은 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-226">We don't recommend this approach.</span></span> <span data-ttu-id="8eb3f-227">이 방법을 사용하려면 퍼블릭 클라이언트에 대해 생성된 것처럼 타사 액세스 토큰을 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-227">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="8eb3f-228">OAuth 맥락에서, 퍼블릭 앱은 암호를 안전하게 저장하는 데 신뢰할 수 없으므로 클라이언트 암호를 갖지 않으며, 액세스 토큰은 기밀 클라이언트에 대해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-228">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="8eb3f-229">기밀 클라이언트는 클라이언트 암호를 포함하는 클라이언트이며 비밀을 안전하게 저장할 수 있는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-229">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="8eb3f-230">타사 액세스 토큰에는 타사에서 더 신뢰할 수 있는 클라이언트에 대한 토큰을 내보낸 사실을 기반으로 중요한 작업을 수행하기 위한 추가 범위가 부여될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-230">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="8eb3f-231">마찬가지로, 신뢰할 수 없는 클라이언트에 대해서는 새로 고침 토큰을 발급하지 않아야 합니다. 발급할 경우, 다른 제한이 적용되지 않는 한 클라이언트에 무제한 액세스 권한이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-231">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="8eb3f-232">타사 API를 호출하기 위해 클라이언트에서 서버 API로 API 호출 수행</span><span class="sxs-lookup"><span data-stu-id="8eb3f-232">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="8eb3f-233">클라이언트에서 서버 API로 API 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-233">Make an API call from the client to the server API.</span></span> <span data-ttu-id="8eb3f-234">서버에서 타사 API 리소스에 대한 액세스 토큰을 검색하고 필요한 호출이 무엇이든 해당 호출을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-234">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="8eb3f-235">이 방법을 사용하려면 타사 API를 호출하기 위해 서버를 통한 추가 네트워크 홉이 필요하지만, 궁극적으로 다음과 같은 더 안전한 환경이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-235">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="8eb3f-236">서버는 새로 고침 토큰을 저장하고 앱이 타사 리소스에 대한 액세스 권한을 잃지 않도록 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-236">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="8eb3f-237">앱은 더 중요한 권한을 포함할 수 있는 서버의 액세스 토큰을 누출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8eb3f-237">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
