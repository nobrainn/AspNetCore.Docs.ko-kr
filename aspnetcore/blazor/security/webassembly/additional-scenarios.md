---
title: ASP.NET Core Blazor WebAssembly 추가 보안 시나리오
author: guardrex
description: 추가 보안 시나리오에 대해 Blazor WebAssembly를 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 0cf2c2d2ef0d199ca5df6c27ddcc39e84db46ebd
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059762"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="29b92-103">ASP.NET Core Blazor WebAssembly 추가 보안 시나리오</span><span class="sxs-lookup"><span data-stu-id="29b92-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="29b92-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29b92-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="29b92-105">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="29b92-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="29b92-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 서비스를 <xref:System.Net.Http.HttpClient>와 함께 사용하여 나가는 요청에 액세스 토큰을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="29b92-107">토큰은 기존 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> 서비스를 사용하여 획득됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="29b92-108">토큰을 획득할 수 없는 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="29b92-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>에는 사용자가 ID 공급자로 이동하여 새 토큰을 획득하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="29b92-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>는 권한 있는 URL을 사용하여 구성되고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 메서드를 사용하여 URL을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="29b92-111">다음 방법 중 하나를 사용하여 보내는 요청에 대한 메시지 처리기를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="29b92-112">[사용자 지정 `AuthorizationMessageHandler` 클래스](#custom-authorizationmessagehandler-class)(‘권장’)</span><span class="sxs-lookup"><span data-stu-id="29b92-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="29b92-113">`AuthorizationMessageHandler` 구성</span><span class="sxs-lookup"><span data-stu-id="29b92-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="29b92-114">사용자 지정 AuthorizationMessageHandler 클래스</span><span class="sxs-lookup"><span data-stu-id="29b92-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="29b92-115">다음 예제에서는 사용자 지정 클래스가 <xref:System.Net.Http.HttpClient>를 구성하는 데 사용할 수 있는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="29b92-116">`Program.Main`(`Program.cs`)에서 사용자 지정 권한 부여 메시지 처리기를 사용하여 <xref:System.Net.Http.HttpClient>가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="29b92-117">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`)를 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="29b92-118">구성된 <xref:System.Net.Http.HttpClient>는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 사용하여 권한이 부여된 요청을 수행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="29b92-119">클라이언트가 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 패키지)를 사용하여 만들어진 경우, <xref:System.Net.Http.HttpClient>에는 서버 API로 요청을 전송할 때 액세스 토큰을 포함하는 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="29b92-120">AuthorizationMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="29b92-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="29b92-121">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>가 `Program.Main`(`Program.cs`)에서 <xref:System.Net.Http.HttpClient>를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri("https://www.example.com/base")
        };
});
```

<span data-ttu-id="29b92-122">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>를 다음에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="29b92-123"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="29b92-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="29b92-124">`authorizedUrls` 배열의 URL.</span><span class="sxs-lookup"><span data-stu-id="29b92-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="29b92-125">편의를 위해, 앱의 기준 주소로 구성된 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>가 권한이 부여된 URL로 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="29b92-126">인증이 사용하도록 설정된 Blazor WebAssembly 템플릿은 서버 API 프로젝트에서 <xref:System.Net.Http.IHttpClientFactory>([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 패키지)를 사용하여 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>로 <xref:System.Net.Http.HttpClient>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="29b92-127">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`)를 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="29b92-128">앞에 나온 예제에서 클라이언트가 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>를 사용하여 만들어진 경우, <xref:System.Net.Http.HttpClient>에는 서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="29b92-129">구성된 <xref:System.Net.Http.HttpClient>는 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 사용하여 권한이 부여된 요청을 수행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="29b92-130">형식화된 HttpClient</span><span class="sxs-lookup"><span data-stu-id="29b92-130">Typed HttpClient</span></span>

<span data-ttu-id="29b92-131">단일 클래스 내에서 모든 HTTP 및 토큰 획득 문제를 처리하는 형식화된 클라이언트를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="29b92-132">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="29b92-132">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="29b92-133">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="29b92-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="29b92-134">`Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="29b92-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="29b92-135">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`)를 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="29b92-136">`FetchData` 구성 요소(`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="29b92-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="29b92-137">HttpClient 처리기 구성</span><span class="sxs-lookup"><span data-stu-id="29b92-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="29b92-138">처리기는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>를 사용하여 아웃바운드 HTTP 요청에 대해 추가로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="29b92-139">`Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="29b92-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="29b92-140">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>를 다음에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="29b92-141"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="29b92-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="29b92-142">`authorizedUrls` 배열의 URL.</span><span class="sxs-lookup"><span data-stu-id="29b92-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="29b92-143">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="29b92-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="29b92-144">Blazor WebAssembly 앱이 일반적으로 보안 기본 <xref:System.Net.Http.HttpClient>를 사용하는 경우 이 앱은 명명된 <xref:System.Net.Http.HttpClient>를 구성하여 인증되지 않거나 권한 부여되지 않은 웹 API 요청을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="29b92-145">`Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="29b92-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="29b92-146">Blazor WebAssembly 호스트형 템플릿을 기반으로 하는 Blazor 앱의 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`new Uri(builder.HostEnvironment.BaseAddress)`)를 <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>에 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="29b92-147">앞에 나온 등록은 기존의 보안 기본 <xref:System.Net.Http.HttpClient> 등록에 더해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="29b92-148">구성 요소는 <xref:System.Net.Http.IHttpClientFactory>([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 패키지)에서 <xref:System.Net.Http.HttpClient>를 만들어 인증되지 않거나 권한이 부여되지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="29b92-149">앞에 나온 예제의 서버 API의 컨트롤러는 `WeatherForecastNoAuthenticationController`는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성으로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="29b92-150">보안 클라이언트 또는 안전하지 않은 클라이언트를 기본 <xref:System.Net.Http.HttpClient> 인스턴스로 사용할지 여부는 개발자가 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="29b92-151">이 결정을 내리는 한 가지 방법은 앱이 연결되는 인증된 엔드포인트와 인증되지 않은 엔드포인트의 수를 고려하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="29b92-152">대다수 앱 요청이 API 엔드포인트를 보호하기 위한 것이면 인증된 <xref:System.Net.Http.HttpClient> 인스턴스를 기본값으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="29b92-153">그렇지 않으면 인증되지 않은 <xref:System.Net.Http.HttpClient> 인스턴스를 기본값으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="29b92-154"><xref:System.Net.Http.IHttpClientFactory>를 사용하는 대체 방법은 익명 엔드포인트에 대한 인증되지 않은 액세스를 위해 [형식화된 클라이언트](#typed-httpclient)를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="29b92-155">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="29b92-155">Request additional access tokens</span></span>

<span data-ttu-id="29b92-156">`IAccessTokenProvider.RequestAccessToken`을 호출하여 액세스 토큰을 수동으로 획득할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="29b92-157">다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내기 위해 추가 AAD(Azure Active Directory) Microsoft Graph API 범위가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="29b92-158">Azure AAD 포털에서 Microsoft Graph API 사용 권한을 추가한 후에 클라이언트 앱에서 추가 범위가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="29b92-159">`Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="29b92-159">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="29b92-160">`IAccessTokenProvider.RequestToken` 메서드는 앱이 지정된 범위 세트를 사용하여 액세스 토큰을 프로비저닝할 수 있도록 지원하는 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="29b92-161">Razor 구성 요소에서:</span><span class="sxs-lookup"><span data-stu-id="29b92-161">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
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

<span data-ttu-id="29b92-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>은 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="29b92-163">`token`이 사용할 수 있도록 `true`.</span><span class="sxs-lookup"><span data-stu-id="29b92-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="29b92-164">토큰이 검색되지 않은 경우 `false`.</span><span class="sxs-lookup"><span data-stu-id="29b92-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="29b92-165">Fetch API 요청 옵션이 포함된 HttpClient 및 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="29b92-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="29b92-166">Blazor WebAssembly 앱의 WebAssembly에서 실행될 경우 [`HttpClient`](xref:fundamentals/http-requests)([API 설명서](xref:System.Net.Http.HttpClient)) 및 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 요청을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="29b92-167">예를 들어, HTTP 메서드 및 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="29b92-168">다음 구성 요소는 서버에서 To Do List API 엔드포인트에 대한 `POST` 요청을 만들고 응답 본문을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="29b92-169">.NET WebAssembly의 <xref:System.Net.Http.HttpClient> 구현은 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="29b92-170">Fetch를 사용하면 여러 [요청 관련 옵션](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="29b92-171">HTTP 페치 요청 옵션은 다음 표에 표시된 <xref:System.Net.Http.HttpRequestMessage> 확장 메서드로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="29b92-172">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="29b92-172">Extension method</span></span> | <span data-ttu-id="29b92-173">Fetch 요청 속성</span><span class="sxs-lookup"><span data-stu-id="29b92-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="29b92-174">보다 일반적인 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 확장 메서드를 사용하여 추가 옵션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="29b92-175">HTTP 응답은 일반적으로 응답 콘텐츠에서 동기 읽기를 지원할 수 있도록 Blazor WebAssembly 앱에서 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="29b92-176">응답 스트리밍을 지원하도록 설정하려면 요청에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="29b92-177">원본 간 요청에 자격 증명을 포함하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="29b92-178">Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29b92-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="29b92-179">CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="29b92-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="29b92-180">CORS 요청에 대한 자격 증명(권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="29b92-181">다음 정책에는 해당 구성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="29b92-182">요청 원본(`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="29b92-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="29b92-183">임의 메서드(동사)</span><span class="sxs-lookup"><span data-stu-id="29b92-183">Any method (verb).</span></span>
* <span data-ttu-id="29b92-184">`Content-Type` 및 `Authorization` 헤더.</span><span class="sxs-lookup"><span data-stu-id="29b92-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="29b92-185">사용자 지정 헤더(예: `x-custom-header`)를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="29b92-186">클라이언트 쪽 JavaScript 코드에 의해 설정된 자격 증명(`credentials` 속성이 `include`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="29b92-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="29b92-187">자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소(`Components/HTTPRequestTester.razor`)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29b92-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="29b92-188">토큰 요청 오류 처리</span><span class="sxs-lookup"><span data-stu-id="29b92-188">Handle token request errors</span></span>

<span data-ttu-id="29b92-189">SPA(단일 페이지 애플리케이션)가 OIDC(Open ID Connect)를 사용하여 사용자를 인증하는 경우 인증 상태는 SPA 내에서 로컬로 유지 관리되는 동시에 IP(Identity 공급자)에서 사용자가 자격 증명을 제공한 결과 설정된 세션 쿠키 형식으로 유지 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="29b92-190">일반적으로 사용자에 대해 IP가 내보내는 토큰은 짧은 시간 동안(보통 1시간 동안) 유효하므로 클라이언트 앱이 정기적으로 새 토큰을 페치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="29b92-191">그러지 않으면 부여된 토큰이 만료된 후에 사용자가 로그아웃됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="29b92-192">대부분의 경우 OIDC 클라이언트는 IP 내에 유지되는 인증 상태 또는 “세션” 덕분에 사용자에게 다시 인증하도록 요구하지 않고 새 토큰을 프로비저닝할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="29b92-193">클라이언트가 사용자 조작 없이는 토큰을 가져올 수 없는 경우도 있습니다. 사용자가 어떤 이유로든 IP에서 명시적으로 로그아웃하는 경우가 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="29b92-194">이 시나리오는 사용자가 `https://login.microsoftonline.com`을 방문한 후 로그아웃하는 경우에 발생합니다. 이러한 시나리오에서 앱은 사용자가 로그아웃했다는 사실을 즉시 인식하지 못합니다. 이때 클라이언트가 보유하고 있는 토큰은 모두 더 이상 유효하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="29b92-195">또한 클라이언트는 현재 토큰이 만료된 후 사용자 조작 없이는 새 토큰을 프로비저닝할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="29b92-196">이러한 시나리오는 토큰 기반 인증에만 국한되지 않으며,</span><span class="sxs-lookup"><span data-stu-id="29b92-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="29b92-197">SPA의 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="29b92-198">쿠키를 사용하는 SPA도 인증 쿠키가 제거된 경우 서버 API를 호출하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="29b92-199">앱에서 보호된 리소스에 대해 API 호출을 수행하는 경우에는 다음 사항에 유의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="29b92-200">새 액세스 토큰을 프로비저닝하여 API를 호출하려면 사용자가 다시 인증해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="29b92-201">클라이언트에 유효한 것으로 보이는 토큰이 있는 경우에도 사용자가 토큰을 해지했기 때문에 서버에 대한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="29b92-202">앱에서 토큰을 요청하는 경우 다음과 같은 두 가지 가능한 결과가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="29b92-203">요청이 성공하고 앱이 유효한 토큰을 보유합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="29b92-204">요청이 실패하고 앱이 사용자를 다시 인증하여 새 토큰을 획득해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="29b92-205">토큰 요청이 실패할 경우 리디렉션을 수행하기 전에 현재 상태를 저장할 것인지 여부를 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="29b92-206">여기에는 다양한 접근 방식이 있으며, 아래로 갈수록 복잡도가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="29b92-207">세션 스토리지에 현재 페이지 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-207">Store the current page state in session storage.</span></span> <span data-ttu-id="29b92-208">[`OnInitializedAsync` 수명 주기 이벤트](xref:blazor/components/lifecycle#component-initialization-methods)(<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 중에 계속하기 전에 상태를 복원할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="29b92-209">쿼리 문자열 매개 변수를 추가하고 이 매개 변수를 이전에 저장된 상태를 다시 하이드레이션해야 한다는 사실을 앱에 알리는 용도로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="29b92-210">고유 식별자를 갖는 쿼리 문자열 매개 변수를 추가하여 다른 항목과의 충돌이 발생할 가능성 없이 세션 스토리지에 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="29b92-211">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-211">The following example shows how to:</span></span>

* <span data-ttu-id="29b92-212">로그인 페이지로 리디렉션하기 전에 상태를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="29b92-213">인증 후에 쿼리 문자열 매개 변수를 사용하여 이전 상태를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="29b92-214">인증 작업 전에 앱 상태 저장</span><span class="sxs-lookup"><span data-stu-id="29b92-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="29b92-215">인증 작업을 수행하는 동안 브라우저가 IP로 리디렉션되기 전에 앱 상태를 저장해야 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="29b92-216">상태 컨테이너를 사용하고 있으며 인증에 성공한 후에 상태를 복원하려는 경우가 여기에 해당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="29b92-217">이때 사용자 지정 인증 상태 개체를 사용하여 앱의 상태를 보존하거나 참조하고 인증 작업이 성공적으로 완료된 후에 해당 상태를 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="29b92-218">다음 예제에서는 이 접근 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="29b92-219">앱의 상태 값을 저장하는 속성이 있는 상태 컨테이너 클래스가 앱 안에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="29b92-220">다음 예제에서는 기본 템플릿의 `Counter` 구성 요소(`Pages/Counter.razor`)의 카운터 값을 유지하기 위해 컨테이너가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="29b92-221">컨테이너를 직렬화 및 역직렬화하는 메서드는 <xref:System.Text.Json>을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="29b92-222">`Counter` 구성 요소는 상태 컨테이너를 사용하여 구성 요소 밖에서 `currentCount` 값을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="29b92-223"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>에서 `ApplicationAuthenticationState`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="29b92-224">로컬로 저장된 상태의 식별자로 기능하는 `Id` 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="29b92-225">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="29b92-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="29b92-226">`Authentication` 구성 요소(`Pages/Authentication.razor`)는 `StateContainer` serialization 및 deserialization 메서드인 `GetStateForLocalStorage` 및 `SetStateFromLocalStorage`로 로컬 세션 스토리지를 사용하여 앱의 상태를 저장하고 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="29b92-227">이 예제에서는 인증을 위해 AAD(Azure Active Directory)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="29b92-228">`Program.Main`(`Program.cs`)에서:</span><span class="sxs-lookup"><span data-stu-id="29b92-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="29b92-229">`ApplicationAuthenticationState`는 MSAL(Microsoft 인증 라이브러리) `RemoteAuthenticationState` 유형으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="29b92-230">상태 컨테이너는 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="29b92-231">앱 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="29b92-231">Customize app routes</span></span>

<span data-ttu-id="29b92-232">기본적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 라이브러리는 다음 표에 나와 있는 경로를 사용하여 다양한 인증 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="29b92-233">경로</span><span class="sxs-lookup"><span data-stu-id="29b92-233">Route</span></span>                            | <span data-ttu-id="29b92-234">용도</span><span class="sxs-lookup"><span data-stu-id="29b92-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="29b92-235">로그인 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="29b92-236">로그인 작업의 결과를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="29b92-237">어떤 이유로든 로그인 작업이 실패한 경우 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="29b92-238">로그아웃 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="29b92-239">로그아웃 작업의 결과를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="29b92-240">어떤 이유로든 로그아웃 작업이 실패한 경우 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="29b92-241">사용자가 성공적으로 로그아웃했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="29b92-242">사용자 프로필을 편집하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="29b92-243">새 사용자를 등록하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="29b92-244">앞의 표에 나와 있는 경로는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>를 통해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="29b92-245">사용자 지정 경로를 제공하는 옵션을 설정할 때는 앱에 각 경로를 처리하는 경로가 있는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="29b92-246">다음 예제에서는 모든 경로에 `/security` 접두사가 붙습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="29b92-247">`Authentication` 구성 요소(`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="29b92-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="29b92-248">`Program.Main`(`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="29b92-248">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="29b92-249">서로 완전히 다른 경로가 필요한 경우, 이전에 설명한 대로 경로를 설정하고 명시적 작업 매개 변수를 사용하여 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="29b92-250">원하는 경우 UI를 여러 페이지로 나눌 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="29b92-251">인증 사용자 인터페이스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="29b92-251">Customize the authentication user interface</span></span>

<span data-ttu-id="29b92-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>에는 각 인증 상태에 대한 기본 UI 조각 집합이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="29b92-253">사용자 지정 <xref:Microsoft.AspNetCore.Components.RenderFragment>를 전달하여 각 상태를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="29b92-254">초기 로그인 프로세스 중에 표시되는 텍스트를 사용자 지정하려면 다음과 같이 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="29b92-255">`Authentication` 구성 요소(`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="29b92-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="29b92-256"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>에는 다음 표에 나와 있는 인증 경로에 따라 사용할 수 있는 하나의 조각이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="29b92-257">경로</span><span class="sxs-lookup"><span data-stu-id="29b92-257">Route</span></span>                            | <span data-ttu-id="29b92-258">Fragment</span><span class="sxs-lookup"><span data-stu-id="29b92-258">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="29b92-259">사용자의 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="29b92-259">Customize the user</span></span>

<span data-ttu-id="29b92-260">앱에 바인딩된 사용자는 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="29b92-261">다음 예제에서는 모든 인증된 사용자가 각 사용자 인증 방법에 대한 `amr` 클레임을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="29b92-262"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 클래스를 확장하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="29b92-263"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>를 확장하는 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="29b92-264">사용 중인 인증 공급자에 대해 `CustomAccountFactory`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="29b92-265">다음 등록이 모두 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="29b92-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="29b92-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="29b92-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="29b92-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="29b92-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="29b92-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="29b92-269">인증을 사용한 미리 렌더링 지원</span><span class="sxs-lookup"><span data-stu-id="29b92-269">Support prerendering with authentication</span></span>

<span data-ttu-id="29b92-270">호스트된 Blazor WebAssembly 앱 항목 중 하나의 지침을 따른 후에는 다음 지침에 따라 다음과 같은 동작을 수행하는 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="29b92-271">인증이 필요하지 않은 경로를 미리 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="29b92-272">인증이 필요한 경로를 미리 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="29b92-273">클라이언트 앱의 `Program` 클래스(`Program.cs`)에서 공통 서비스 등록을 별도의 메서드(예: `ConfigureCommonServices`)로 팩터링합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

<span data-ttu-id="29b92-274">서버 앱의 `Startup.ConfigureServices`에서 다음과 같은 추가 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="29b92-275">서버 앱의 `Startup.Configure` 메서드에서 [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A)을 [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="29b92-276">서버 앱에서 `Pages` 폴더가 없으면 이 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="29b92-277">서버 앱의 `Pages` 폴더 안에 `_Host.cshtml` 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="29b92-278">클라이언트 앱의 `wwwroot/index.html` 파일 콘텐츠를 `Pages/_Host.cshtml` 파일에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="29b92-279">다음과 같이 파일 콘텐츠를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-279">Update the file's contents:</span></span>

* <span data-ttu-id="29b92-280">`@page "_Host"`를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="29b92-281">`<app>Loading...</app>` 태그를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="29b92-282">호스트된 앱 및 타사 로그인 공급자에 대한 옵션</span><span class="sxs-lookup"><span data-stu-id="29b92-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="29b92-283">타사 공급자를 사용하여 호스트된 Blazor WebAssembly 앱을 인증하고 권한을 부여하는 경우 사용자를 인증하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="29b92-284">시나리오에 따라 선택하는 옵션이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="29b92-285">자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29b92-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="29b92-286">보호된 타사 API만 호출하도록 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="29b92-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="29b92-287">클라이언트 쪽 OAuth 흐름을 사용하여 사용자를 타사 API 공급자에 대해 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="29b92-288">이 시나리오에서는</span><span class="sxs-lookup"><span data-stu-id="29b92-288">In this scenario:</span></span>

* <span data-ttu-id="29b92-289">앱을 호스트하는 서버에서 역할을 재생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="29b92-290">서버의 API는 보호할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="29b92-291">앱은 보호된 타사 API만 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="29b92-292">타사 공급자를 사용하여 사용자를 인증하고 호스트 서버 및 타사에서 보호된 API 호출</span><span class="sxs-lookup"><span data-stu-id="29b92-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="29b92-293">타사 로그인 공급자를 사용하여 Identity를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="29b92-294">타사 API 액세스에 필요한 토큰을 가져와 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="29b92-295">사용자가 로그인하면 Identity는 인증 프로세스의 일부로 액세스 및 새로 고침 토큰을 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="29b92-296">이때 타사 API에 대한 API 호출을 수행하는 데 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="29b92-297">서버 액세스 토큰을 사용하여 타사 액세스 토큰 검색</span><span class="sxs-lookup"><span data-stu-id="29b92-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="29b92-298">서버에서 생성된 액세스 토큰을 사용하여 서버 API 엔드포인트에서 타사 액세스 토큰을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="29b92-299">여기에서 타사 액세스 토큰을 사용하여 클라이언트의 Identity에서 직접 타사 API 리소스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="29b92-300">이 방법은 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-300">We don't recommend this approach.</span></span> <span data-ttu-id="29b92-301">이 방법을 사용하려면 퍼블릭 클라이언트에 대해 생성된 것처럼 타사 액세스 토큰을 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="29b92-302">OAuth 맥락에서, 퍼블릭 앱은 암호를 안전하게 저장하는 데 신뢰할 수 없으므로 클라이언트 암호를 갖지 않으며, 액세스 토큰은 기밀 클라이언트에 대해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="29b92-303">기밀 클라이언트는 클라이언트 암호를 포함하는 클라이언트이며 비밀을 안전하게 저장할 수 있는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="29b92-304">타사 액세스 토큰에는 타사에서 더 신뢰할 수 있는 클라이언트에 대한 토큰을 내보낸 사실을 기반으로 중요한 작업을 수행하기 위한 추가 범위가 부여될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="29b92-305">마찬가지로, 신뢰할 수 없는 클라이언트에 대해서는 새로 고침 토큰을 발급하지 않아야 합니다. 발급할 경우, 다른 제한이 적용되지 않는 한 클라이언트에 무제한 액세스 권한이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="29b92-306">타사 API를 호출하기 위해 클라이언트에서 서버 API로 API 호출 수행</span><span class="sxs-lookup"><span data-stu-id="29b92-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="29b92-307">클라이언트에서 서버 API로 API 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="29b92-308">서버에서 타사 API 리소스에 대한 액세스 토큰을 검색하고 필요한 호출이 무엇이든 해당 호출을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="29b92-309">이 방법을 사용하려면 타사 API를 호출하기 위해 서버를 통한 추가 네트워크 홉이 필요하지만, 궁극적으로 다음과 같은 더 안전한 환경이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="29b92-310">서버는 새로 고침 토큰을 저장하고 앱이 타사 리소스에 대한 액세스 권한을 잃지 않도록 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="29b92-311">앱은 더 중요한 권한을 포함할 수 있는 서버의 액세스 토큰을 누출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="29b92-312">OIDC(Open ID Connect) v2.0 엔드포인트 사용</span><span class="sxs-lookup"><span data-stu-id="29b92-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="29b92-313">인증 라이브러리 및 Blazor 템플릿은 OIDC(Open ID Connect) v1.0 엔드포인트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="29b92-314">v2.0 엔드포인트를 사용하려면 JWT 전달자 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 옵션을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="29b92-315">다음 예제에서는 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> 속성에 `v2.0` 세그먼트를 추가하여 v2.0에 대해 AAD를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="29b92-316">또는 앱 설정(`appsettings.json`) 파일에서 설정을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="29b92-317">인증 기관에 대한 세그먼트의 추적이 앱의 OIDC 공급자에 적합하지 않은 경우(예: 비 AAD 공급자) <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="29b92-318"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 또는 앱 설정 파일(`appsettings.json`)에서 `Authority` 키를 사용하여 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="29b92-319">ID 토큰의 클레임 목록은 v2.0 엔드포인트의 경우 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="29b92-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="29b92-320">자세한 내용은 [Microsoft ID 플랫폼(v2.0)으로 업데이트해야 하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="29b92-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
