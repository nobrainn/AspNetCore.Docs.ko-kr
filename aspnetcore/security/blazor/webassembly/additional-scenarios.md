---
<span data-ttu-id="17bea-101">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-103">'Blazor'</span></span>
- <span data-ttu-id="17bea-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-104">'Identity'</span></span>
- <span data-ttu-id="17bea-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-106">'Razor'</span></span>
- <span data-ttu-id="17bea-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="17bea-108">Blazor추가 보안 시나리오 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17bea-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="17bea-109">작성자: [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="17bea-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="17bea-110">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="17bea-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="17bea-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>서비스를와 함께 사용 <xref:System.Net.Http.HttpClient> 하 여 액세스 토큰을 보내는 요청에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="17bea-112">토큰은 기존 서비스를 사용 하 여 획득 됩니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> .</span><span class="sxs-lookup"><span data-stu-id="17bea-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="17bea-113">토큰을 가져올 수 없는 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 이 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="17bea-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>에는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 사용자가 id 공급자로 이동 하 여 새 토큰을 획득 하는 데 사용할 수 있는 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="17bea-115">는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 메서드를 사용 하 여 권한 있는 url, 범위 및 반환 URL을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="17bea-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="17bea-116">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> `Program.Main` (*Program.cs*)에서를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
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

<span data-ttu-id="17bea-117">편의상 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 앱 기준 주소를 권한 있는 URL로 미리 구성 된가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="17bea-118">이제 인증 사용 Blazor weasembomomtemplate이 <xref:System.Net.Http.IHttpClientFactory> 서버 API 프로젝트에서를 사용 하 여를 설정 합니다 <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> .</span><span class="sxs-lookup"><span data-stu-id="17bea-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="17bea-119">앞의 예제에서 클라이언트를 만든 경우 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> 서버 프로젝트에 요청을 수행할 때 액세스 토큰이 포함 된 인스턴스가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="17bea-120">그런 다음 구성 된를 사용 하 여 <xref:System.Net.Http.HttpClient> 간단한 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 통해 권한 있는 요청을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="17bea-121">`FetchData` 구성 요소(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="17bea-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="17bea-122">형식화된 HttpClient</span><span class="sxs-lookup"><span data-stu-id="17bea-122">Typed HttpClient</span></span>

<span data-ttu-id="17bea-123">단일 클래스 내에서 모든 HTTP 및 토큰 획득 문제를 처리 하는 형식화 된 클라이언트를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="17bea-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="17bea-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="17bea-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="17bea-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="17bea-126">`FetchData` 구성 요소(*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="17bea-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="17bea-127">HttpClient 처리기 구성</span><span class="sxs-lookup"><span data-stu-id="17bea-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="17bea-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>아웃 바운드 HTTP 요청에 대해를 사용 하 여 처리기를 추가로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="17bea-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="17bea-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="17bea-130">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="17bea-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="17bea-131">BlazorWeasembomapp이 일반적으로 보안 기본값을 사용 하는 경우 <xref:System.Net.Http.HttpClient> 앱은 명명 된를 구성 하 여 인증 되지 않은 웹 API 요청 또는 인증 되지 않은 <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="17bea-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="17bea-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="17bea-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="17bea-133">위의 등록은 기존 보안 기본 등록에 추가 됩니다 <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="17bea-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="17bea-134">구성 요소는 <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 인증 되지 않거나 권한이 없는 요청을 만들기 위해에서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="17bea-135">`WeatherForecastNoAuthenticationController`이전 예제에 대 한 서버 API의 컨트롤러는 특성으로 표시 되지 않습니다 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="17bea-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="17bea-136">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="17bea-136">Request additional access tokens</span></span>

<span data-ttu-id="17bea-137">액세스 토큰은를 호출 하 여 수동으로 가져올 수 있습니다 `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="17bea-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="17bea-138">다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내기 위해 AAD (추가 Azure Active Directory) Microsoft Graph API 범위가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="17bea-139">Azure AAD 포털에서 Microsoft Graph API 사용 권한을 추가한 후 클라이언트 앱에서 추가 범위가 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="17bea-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="17bea-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="17bea-141">`IAccessTokenProvider.RequestToken`메서드는 앱이 지정 된 범위 집합을 사용 하 여 액세스 토큰을 프로 비전 할 수 있도록 하는 오버 로드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="17bea-142">Razor구성 요소에서:</span><span class="sxs-lookup"><span data-stu-id="17bea-142">In a Razor component:</span></span>

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

<span data-ttu-id="17bea-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>전용인</span><span class="sxs-lookup"><span data-stu-id="17bea-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="17bea-144">`true`사용할를 `token` 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="17bea-145">`false`토큰이 검색 되지 않으면입니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="17bea-146">Fetch API 요청 옵션이 포함된 HttpClient 및 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="17bea-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="17bea-147">Weasembomambomapp에서 제대로 실행 되는 경우 Blazor [httpclient](xref:fundamentals/http-requests) 를 <xref:System.Net.Http.HttpRequestMessage> 사용 하 여 요청을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="17bea-148">예를 들어 HTTP 메서드 및 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="17bea-149">다음 구성 요소는 `POST` 서버에서 To Do LIST API 끝점에 대 한 요청을 수행 하 고 응답 본문을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@_responseBody</p>

@code {
    private string _responseBody;

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

            _responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="17bea-150">.NET WebAssembly의 <xref:System.Net.Http.HttpClient> 구현은 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="17bea-151">Fetch를 사용하면 여러 [요청 관련 옵션](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="17bea-152">HTTP 페치 요청 옵션은 다음 표에 표시된 <xref:System.Net.Http.HttpRequestMessage> 확장 메서드로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="17bea-153">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="17bea-153">Extension method</span></span> | <span data-ttu-id="17bea-154">Fetch 요청 속성</span><span class="sxs-lookup"><span data-stu-id="17bea-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="17bea-155">credentials</span><span class="sxs-lookup"><span data-stu-id="17bea-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="17bea-156">캐시</span><span class="sxs-lookup"><span data-stu-id="17bea-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="17bea-157">mode</span><span class="sxs-lookup"><span data-stu-id="17bea-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="17bea-158">무결성</span><span class="sxs-lookup"><span data-stu-id="17bea-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="17bea-159">보다 일반적인 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 확장 메서드를 사용하여 추가 옵션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="17bea-160">HTTP 응답은 일반적으로 응답 콘텐츠에서 동기화 읽기를 지원할 수 있도록 Blazor WebAssembly 앱에서 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="17bea-161">응답 스트리밍을 지원하도록 설정하려면 요청에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="17bea-162">원본 간 요청에 자격 증명을 포함하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="17bea-163">Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17bea-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="17bea-164">CORS 요청에 대한 자격 증명(권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="17bea-165">다음 정책에는 해당 구성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="17bea-166">요청 원본(`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="17bea-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="17bea-167">임의 메서드(동사)</span><span class="sxs-lookup"><span data-stu-id="17bea-167">Any method (verb).</span></span>
* <span data-ttu-id="17bea-168">`Content-Type` 및 `Authorization` 헤더.</span><span class="sxs-lookup"><span data-stu-id="17bea-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="17bea-169">사용자 지정 헤더(예: `x-custom-header`)를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="17bea-170">클라이언트 쪽 JavaScript 코드에 의해 설정된 자격 증명(`credentials` 속성이 `include`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="17bea-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="17bea-171">자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소(*Components/HTTPRequestTester.razor*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17bea-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="17bea-172">토큰 요청 오류 처리</span><span class="sxs-lookup"><span data-stu-id="17bea-172">Handle token request errors</span></span>

<span data-ttu-id="17bea-173">SPA (단일 페이지 응용 프로그램)가 OIDC (Open ID Connect)를 사용 하 여 사용자를 인증 하는 경우 인증 상태는 해당 Identity 자격 증명을 제공 하는 사용자의 결과로 설정 된 세션 쿠키의 형태로 spa 및 공급자 (IP) 내에서 로컬로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="17bea-174">일반적으로 사용자에 대 한 IP가 내보내는 토큰은 짧은 시간 동안 일반적으로 1 시간 동안 유효 하므로 클라이언트 앱이 정기적으로 새 토큰을 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="17bea-175">그렇지 않으면 부여 된 토큰이 만료 된 후 사용자가 로그 아웃 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="17bea-176">대부분의 경우 OIDC 클라이언트는 사용자가 IP 내에 유지 되는 인증 상태 또는 "세션"으로 인해 다시 인증 하도록 요구 하지 않고 새 토큰을 프로 비전 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="17bea-177">사용자가 사용자 개입 없이 토큰을 가져올 수 없는 경우도 있습니다. 예를 들어 사용자가 IP에서 명시적으로 로그 아웃 하는 이유가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="17bea-178">이 시나리오는 사용자가 방문 하 여 로그 아웃 하는 경우에 발생 `https://login.microsoftonline.com` 합니다. 이러한 시나리오에서 앱은 사용자가 로그 아웃 한 즉시 인식 하지 못합니다. 클라이언트에서 보유 하는 모든 토큰은 더 이상 유효 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="17bea-179">또한 클라이언트는 현재 토큰이 만료 된 후 사용자 상호 작용 없이 새 토큰을 프로 비전 할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="17bea-180">이러한 시나리오는 토큰 기반 인증에 국한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="17bea-181">이러한 특성은 SPAs 특성의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="17bea-182">쿠키를 사용 하는 SPA는 인증 쿠키를 제거 하는 경우에도 서버 API를 호출 하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="17bea-183">앱에서 보호 된 리소스에 대 한 API 호출을 수행 하는 경우 다음 사항을 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="17bea-184">새 액세스 토큰을 프로 비전 하 여 API를 호출 하려면 사용자가 다시 인증 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="17bea-185">클라이언트에 유효한 토큰이 있는 경우에도 토큰을 사용자가 해지 했기 때문에 서버에 대 한 호출이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="17bea-186">앱에서 토큰을 요청 하는 경우 다음과 같은 두 가지 결과를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="17bea-187">요청이 성공 하 고 앱에 유효한 토큰이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="17bea-188">요청이 실패 하 고 앱이 사용자를 다시 인증 하 여 새 토큰을 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="17bea-189">토큰 요청이 실패 하는 경우 리디렉션을 수행 하기 전에 현재 상태를 저장할 것인지 여부를 결정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="17bea-190">복잡성 수준이 증가 하는 방법에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="17bea-191">세션 저장소에 현재 페이지 상태를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-191">Store the current page state in session storage.</span></span> <span data-ttu-id="17bea-192">[Oninitializedasync 수명 주기 이벤트](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) 중에 상태를 복원할 수 있는지 확인 한 다음 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="17bea-193">쿼리 문자열 매개 변수를 추가 하 고이 매개 변수를 사용 하 여 이전에 저장 된 상태를 다시 하이드레이션 하며 나중 하는 데 필요한 것으로 앱에 신호를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="17bea-194">다른 항목과 충돌을 발생 시 키 지 않고 세션 저장소에 데이터를 저장 하는 고유 식별자를 사용 하 여 쿼리 문자열 매개 변수를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="17bea-195">아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-195">The following example shows how to:</span></span>

* <span data-ttu-id="17bea-196">로그인 페이지로 리디렉션하기 전에 상태를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="17bea-197">쿼리 문자열 매개 변수를 사용 하 여 인증 이후 이전 상태를 복구 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="17bea-198">인증 작업 전에 앱 상태 저장</span><span class="sxs-lookup"><span data-stu-id="17bea-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="17bea-199">인증 작업을 수행 하는 동안 브라우저가 IP로 리디렉션되도록 응용 프로그램 상태를 저장 하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="17bea-200">상태 컨테이너와 같은 항목을 사용 하 고 인증에 성공 하 고 나면 상태를 복원 하려는 경우를 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="17bea-201">사용자 지정 인증 상태 개체를 사용 하 여 앱 특정 상태 또는 참조를 보존 하 고 인증 작업이 성공적으로 완료 되 면 해당 상태를 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="17bea-202">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="17bea-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="17bea-203">앱 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="17bea-203">Customize app routes</span></span>

<span data-ttu-id="17bea-204">기본적으로 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 라이브러리는 다음 표에 나와 있는 경로를 사용 하 여 서로 다른 인증 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="17bea-205">경로</span><span class="sxs-lookup"><span data-stu-id="17bea-205">Route</span></span>                            | <span data-ttu-id="17bea-206">목적</span><span class="sxs-lookup"><span data-stu-id="17bea-206">Purpose</span></span> |
| ---
<span data-ttu-id="17bea-207">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-209">'Blazor'</span></span>
- <span data-ttu-id="17bea-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-210">'Identity'</span></span>
- <span data-ttu-id="17bea-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-212">'Razor'</span></span>
- <span data-ttu-id="17bea-213">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-214">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-216">'Blazor'</span></span>
- <span data-ttu-id="17bea-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-217">'Identity'</span></span>
- <span data-ttu-id="17bea-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-219">'Razor'</span></span>
- <span data-ttu-id="17bea-220">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-221">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-223">'Blazor'</span></span>
- <span data-ttu-id="17bea-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-224">'Identity'</span></span>
- <span data-ttu-id="17bea-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-226">'Razor'</span></span>
- <span data-ttu-id="17bea-227">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-228">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-230">'Blazor'</span></span>
- <span data-ttu-id="17bea-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-231">'Identity'</span></span>
- <span data-ttu-id="17bea-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-233">'Razor'</span></span>
- <span data-ttu-id="17bea-234">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-235">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-237">'Blazor'</span></span>
- <span data-ttu-id="17bea-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-238">'Identity'</span></span>
- <span data-ttu-id="17bea-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-240">'Razor'</span></span>
- <span data-ttu-id="17bea-241">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-242">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-244">'Blazor'</span></span>
- <span data-ttu-id="17bea-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-245">'Identity'</span></span>
- <span data-ttu-id="17bea-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-247">'Razor'</span></span>
- <span data-ttu-id="17bea-248">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-249">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-251">'Blazor'</span></span>
- <span data-ttu-id="17bea-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-252">'Identity'</span></span>
- <span data-ttu-id="17bea-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-254">'Razor'</span></span>
- <span data-ttu-id="17bea-255">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-256">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-258">'Blazor'</span></span>
- <span data-ttu-id="17bea-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-259">'Identity'</span></span>
- <span data-ttu-id="17bea-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-261">'Razor'</span></span>
- <span data-ttu-id="17bea-262">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-263">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-265">'Blazor'</span></span>
- <span data-ttu-id="17bea-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-266">'Identity'</span></span>
- <span data-ttu-id="17bea-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-268">'Razor'</span></span>
- <span data-ttu-id="17bea-269">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-270">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-272">'Blazor'</span></span>
- <span data-ttu-id="17bea-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-273">'Identity'</span></span>
- <span data-ttu-id="17bea-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-275">'Razor'</span></span>
- <span data-ttu-id="17bea-276">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-277">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-279">'Blazor'</span></span>
- <span data-ttu-id="17bea-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-280">'Identity'</span></span>
- <span data-ttu-id="17bea-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-282">'Razor'</span></span>
- <span data-ttu-id="17bea-283">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-284">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-286">'Blazor'</span></span>
- <span data-ttu-id="17bea-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-287">'Identity'</span></span>
- <span data-ttu-id="17bea-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-289">'Razor'</span></span>
- <span data-ttu-id="17bea-290">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-291">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-293">'Blazor'</span></span>
- <span data-ttu-id="17bea-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-294">'Identity'</span></span>
- <span data-ttu-id="17bea-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-296">'Razor'</span></span>
- <span data-ttu-id="17bea-297">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-298">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-300">'Blazor'</span></span>
- <span data-ttu-id="17bea-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-301">'Identity'</span></span>
- <span data-ttu-id="17bea-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-303">'Razor'</span></span>
- <span data-ttu-id="17bea-304">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-304">'SignalR' uid:</span></span> 

<span data-ttu-id="17bea-305">---------------- | ---제목: ' ASP.NET Core Blazor weasembmbambaoma 추가 보안 시나리오 ' 작성자: 설명: ' Blazor 추가 보안 시나리오를 위해 weasembmbomommbmbommbmbmboms</span><span class="sxs-lookup"><span data-stu-id="17bea-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-307">'Blazor'</span></span>
- <span data-ttu-id="17bea-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-308">'Identity'</span></span>
- <span data-ttu-id="17bea-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-310">'Razor'</span></span>
- <span data-ttu-id="17bea-311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-311">'SignalR' uid:</span></span> 

<span data-ttu-id="17bea-312">---- | | `authentication/login`           | 로그인 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="17bea-313">| | `authentication/login-callback`  | 로그인 작업의 결과를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="17bea-314">| | `authentication/login-failed`    | 어떤 이유로 인해 로그인 작업이 실패 하는 경우 오류 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="17bea-315">| | `authentication/logout`          | 로그 아웃 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="17bea-316">| | `authentication/logout-callback` | 로그 아웃 작업의 결과를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="17bea-317">| | `authentication/logout-failed`   | 어떤 이유로 인해 로그 아웃 작업이 실패 하는 경우 오류 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="17bea-318">| | `authentication/logged-out`      | 사용자가 성공적으로 로그 아웃 했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="17bea-319">| | `authentication/profile`         | 사용자 프로필을 편집 하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="17bea-320">| | `authentication/register`        | 새 사용자를 등록 하는 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="17bea-321">위의 표에 표시 된 경로는를 통해 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="17bea-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="17bea-322">사용자 지정 경로를 제공 하는 옵션을 설정 하는 경우 앱에 각 경로를 처리 하는 경로가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="17bea-323">다음 예제에서 모든 경로에는 접두사가 붙습니다 `/security` .</span><span class="sxs-lookup"><span data-stu-id="17bea-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="17bea-324">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="17bea-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="17bea-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="17bea-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="17bea-326">요구 사항이 완전히 다른 경로에 대해를 호출 하는 경우 앞에서 설명한 대로 경로를 설정 하 고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 명시적인 동작 매개 변수를 사용 하 여을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="17bea-327">이렇게 선택 하면 UI를 다른 페이지로 나눌 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="17bea-328">인증 사용자 인터페이스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="17bea-328">Customize the authentication user interface</span></span>

<span data-ttu-id="17bea-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>에는 각 인증 상태에 대 한 기본 UI 조각 집합이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="17bea-330">사용자 지정를 전달 하 여 각 상태를 사용자 지정할 수 있습니다 <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="17bea-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="17bea-331">초기 로그인 프로세스 중에 표시 되는 텍스트를 사용자 지정 하려면에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 다음과 같이 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="17bea-332">`Authentication`구성 요소 (*페이지/인증. razor*):</span><span class="sxs-lookup"><span data-stu-id="17bea-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="17bea-333">에는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 다음 표에 표시 된 인증 경로 별로 사용할 수 있는 하나의 조각이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="17bea-334">경로</span><span class="sxs-lookup"><span data-stu-id="17bea-334">Route</span></span>                            | <span data-ttu-id="17bea-335">조각</span><span class="sxs-lookup"><span data-stu-id="17bea-335">Fragment</span></span>                |
| ---
<span data-ttu-id="17bea-336">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-338">'Blazor'</span></span>
- <span data-ttu-id="17bea-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-339">'Identity'</span></span>
- <span data-ttu-id="17bea-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-341">'Razor'</span></span>
- <span data-ttu-id="17bea-342">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-343">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-345">'Blazor'</span></span>
- <span data-ttu-id="17bea-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-346">'Identity'</span></span>
- <span data-ttu-id="17bea-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-348">'Razor'</span></span>
- <span data-ttu-id="17bea-349">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-350">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-352">'Blazor'</span></span>
- <span data-ttu-id="17bea-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-353">'Identity'</span></span>
- <span data-ttu-id="17bea-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-355">'Razor'</span></span>
- <span data-ttu-id="17bea-356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-357">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-359">'Blazor'</span></span>
- <span data-ttu-id="17bea-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-360">'Identity'</span></span>
- <span data-ttu-id="17bea-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-362">'Razor'</span></span>
- <span data-ttu-id="17bea-363">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-364">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-366">'Blazor'</span></span>
- <span data-ttu-id="17bea-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-367">'Identity'</span></span>
- <span data-ttu-id="17bea-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-369">'Razor'</span></span>
- <span data-ttu-id="17bea-370">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-371">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-373">'Blazor'</span></span>
- <span data-ttu-id="17bea-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-374">'Identity'</span></span>
- <span data-ttu-id="17bea-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-376">'Razor'</span></span>
- <span data-ttu-id="17bea-377">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-378">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-380">'Blazor'</span></span>
- <span data-ttu-id="17bea-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-381">'Identity'</span></span>
- <span data-ttu-id="17bea-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-383">'Razor'</span></span>
- <span data-ttu-id="17bea-384">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-385">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-387">'Blazor'</span></span>
- <span data-ttu-id="17bea-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-388">'Identity'</span></span>
- <span data-ttu-id="17bea-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-390">'Razor'</span></span>
- <span data-ttu-id="17bea-391">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-392">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-394">'Blazor'</span></span>
- <span data-ttu-id="17bea-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-395">'Identity'</span></span>
- <span data-ttu-id="17bea-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-397">'Razor'</span></span>
- <span data-ttu-id="17bea-398">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-399">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-401">'Blazor'</span></span>
- <span data-ttu-id="17bea-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-402">'Identity'</span></span>
- <span data-ttu-id="17bea-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-404">'Razor'</span></span>
- <span data-ttu-id="17bea-405">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-406">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-408">'Blazor'</span></span>
- <span data-ttu-id="17bea-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-409">'Identity'</span></span>
- <span data-ttu-id="17bea-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-411">'Razor'</span></span>
- <span data-ttu-id="17bea-412">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-413">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-415">'Blazor'</span></span>
- <span data-ttu-id="17bea-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-416">'Identity'</span></span>
- <span data-ttu-id="17bea-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-418">'Razor'</span></span>
- <span data-ttu-id="17bea-419">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-420">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-422">'Blazor'</span></span>
- <span data-ttu-id="17bea-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-423">'Identity'</span></span>
- <span data-ttu-id="17bea-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-425">'Razor'</span></span>
- <span data-ttu-id="17bea-426">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-427">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-429">'Blazor'</span></span>
- <span data-ttu-id="17bea-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-430">'Identity'</span></span>
- <span data-ttu-id="17bea-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-432">'Razor'</span></span>
- <span data-ttu-id="17bea-433">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-433">'SignalR' uid:</span></span> 

<span data-ttu-id="17bea-434">---------------- | ---제목: ' ASP.NET Core Blazor weasembmbambaoma 추가 보안 시나리오 ' 작성자: 설명: ' Blazor 추가 보안 시나리오를 위해 weasembmbomommbmbommbmbmboms</span><span class="sxs-lookup"><span data-stu-id="17bea-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-436">'Blazor'</span></span>
- <span data-ttu-id="17bea-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-437">'Identity'</span></span>
- <span data-ttu-id="17bea-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-439">'Razor'</span></span>
- <span data-ttu-id="17bea-440">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-441">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-443">'Blazor'</span></span>
- <span data-ttu-id="17bea-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-444">'Identity'</span></span>
- <span data-ttu-id="17bea-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-446">'Razor'</span></span>
- <span data-ttu-id="17bea-447">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-448">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-450">'Blazor'</span></span>
- <span data-ttu-id="17bea-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-451">'Identity'</span></span>
- <span data-ttu-id="17bea-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-453">'Razor'</span></span>
- <span data-ttu-id="17bea-454">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-455">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-457">'Blazor'</span></span>
- <span data-ttu-id="17bea-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-458">'Identity'</span></span>
- <span data-ttu-id="17bea-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-460">'Razor'</span></span>
- <span data-ttu-id="17bea-461">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-462">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-464">'Blazor'</span></span>
- <span data-ttu-id="17bea-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-465">'Identity'</span></span>
- <span data-ttu-id="17bea-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-467">'Razor'</span></span>
- <span data-ttu-id="17bea-468">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-469">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-471">'Blazor'</span></span>
- <span data-ttu-id="17bea-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-472">'Identity'</span></span>
- <span data-ttu-id="17bea-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-474">'Razor'</span></span>
- <span data-ttu-id="17bea-475">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-476">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-478">'Blazor'</span></span>
- <span data-ttu-id="17bea-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-479">'Identity'</span></span>
- <span data-ttu-id="17bea-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-481">'Razor'</span></span>
- <span data-ttu-id="17bea-482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-483">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-485">'Blazor'</span></span>
- <span data-ttu-id="17bea-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-486">'Identity'</span></span>
- <span data-ttu-id="17bea-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-488">'Razor'</span></span>
- <span data-ttu-id="17bea-489">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17bea-490">제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor</span><span class="sxs-lookup"><span data-stu-id="17bea-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="17bea-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17bea-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="17bea-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17bea-492">'Blazor'</span></span>
- <span data-ttu-id="17bea-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17bea-493">'Identity'</span></span>
- <span data-ttu-id="17bea-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17bea-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="17bea-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17bea-495">'Razor'</span></span>
- <span data-ttu-id="17bea-496">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="17bea-496">'SignalR' uid:</span></span> 

<span data-ttu-id="17bea-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="17bea-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="17bea-498">사용자 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="17bea-498">Customize the user</span></span>

<span data-ttu-id="17bea-499">앱에 바인딩된 사용자를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="17bea-500">다음 예에서는 인증 된 모든 사용자가 `amr` 각 사용자의 인증 방법에 대 한 클레임을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="17bea-501">클래스를 확장 하는 클래스를 만듭니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="17bea-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="17bea-502">다음을 확장 하는 팩터리를 만듭니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="17bea-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="17bea-503">`CustomAccountFactory`사용 중인 인증 공급자에 대 한를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="17bea-504">유효한 등록은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="17bea-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="17bea-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="17bea-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="17bea-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="17bea-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="17bea-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="17bea-508">인증을 사용한 미리 렌더링 지원</span><span class="sxs-lookup"><span data-stu-id="17bea-508">Support prerendering with authentication</span></span>

<span data-ttu-id="17bea-509">호스트된 Blazor WebAssembly 앱 토픽 중 하나의 지침을 따른 후에는 다음 지침에 따라 다음과 같은 동작을 수행하는 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="17bea-510">인증이 필요하지 않은 경로를 미리 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="17bea-511">인증이 필요한 경로를 미리 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="17bea-512">클라이언트 앱의 `Program` 클래스(*Program.cs*)에서 공통 서비스 등록을 별도의 메서드(예: `ConfigureCommonServices`)로 팩터링합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="17bea-513">서버 앱의 `Startup.ConfigureServices`에서 다음과 같은 추가 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="17bea-514">서버 앱의 `Startup.Configure` 메서드에서 끝점을 대체 [합니다. 끝점을 사용 하는 MapFallbackToFile ("index .html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [ MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="17bea-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="17bea-515">서버 앱에서 *Pages* 폴더가 없으면 이 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="17bea-516">서버 앱의 *Pages* 폴더 안에 *_Host.cshtml* 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="17bea-517">클라이언트 앱의 *wwwroot/index.html* 파일 콘텐츠를 *Pages/_Host.cshtml* 파일에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="17bea-518">다음과 같이 파일 콘텐츠를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-518">Update the file's contents:</span></span>

* <span data-ttu-id="17bea-519">`@page "_Host"`를 파일의 맨 위에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="17bea-520">`<app>Loading...</app>` 태그를 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="17bea-521">호스트된 앱 및 타사 로그인 공급자에 대한 옵션</span><span class="sxs-lookup"><span data-stu-id="17bea-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="17bea-522">타사 공급자를 사용하여 호스트된 Blazor WebAssembly 앱을 인증하고 권한을 부여하는 경우 사용자를 인증하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="17bea-523">시나리오에 따라 선택하는 옵션이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="17bea-524">자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17bea-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="17bea-525">보호된 타사 API만 호출하도록 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="17bea-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="17bea-526">클라이언트 쪽 OAuth 흐름을 사용하여 사용자를 타사 API 공급자에 대해 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="17bea-527">이 시나리오에서는</span><span class="sxs-lookup"><span data-stu-id="17bea-527">In this scenario:</span></span>

* <span data-ttu-id="17bea-528">앱을 호스트하는 서버에서 역할을 재생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="17bea-529">서버의 API는 보호할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="17bea-530">앱은 보호된 타사 API만 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="17bea-531">타사 공급자를 사용하여 사용자를 인증하고 호스트 서버 및 타사에서 보호된 API 호출</span><span class="sxs-lookup"><span data-stu-id="17bea-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="17bea-532">Identity타사 로그인 공급자를 사용 하 여를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="17bea-533">타사 API 액세스에 필요한 토큰을 가져와 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="17bea-534">사용자가 로그인 할 때는 Identity 인증 프로세스의 일부로 액세스 및 새로 고침 토큰을 수집 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="17bea-535">이때 타사 API에 대한 API 호출을 수행하는 데 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="17bea-536">서버 액세스 토큰을 사용하여 타사 액세스 토큰 검색</span><span class="sxs-lookup"><span data-stu-id="17bea-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="17bea-537">서버에서 생성된 액세스 토큰을 사용하여 서버 API 엔드포인트에서 타사 액세스 토큰을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="17bea-538">여기에서 타사 액세스 토큰을 사용 하 여 클라이언트에서 직접 타사 API 리소스를 호출 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="17bea-539">이 방법은 사용하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-539">We don't recommend this approach.</span></span> <span data-ttu-id="17bea-540">이 방법을 사용하려면 퍼블릭 클라이언트에 대해 생성된 것처럼 타사 액세스 토큰을 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="17bea-541">OAuth 맥락에서, 퍼블릭 앱은 암호를 안전하게 저장하는 데 신뢰할 수 없으므로 클라이언트 암호를 갖지 않으며, 액세스 토큰은 기밀 클라이언트에 대해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="17bea-542">기밀 클라이언트는 클라이언트 암호를 포함하는 클라이언트이며 비밀을 안전하게 저장할 수 있는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="17bea-543">타사 액세스 토큰에는 타사에서 더 신뢰할 수 있는 클라이언트에 대한 토큰을 내보낸 사실을 기반으로 중요한 작업을 수행하기 위한 추가 범위가 부여될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="17bea-544">마찬가지로, 신뢰할 수 없는 클라이언트에 대해서는 새로 고침 토큰을 발급하지 않아야 합니다. 발급할 경우, 다른 제한이 적용되지 않는 한 클라이언트에 무제한 액세스 권한이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="17bea-545">타사 API를 호출하기 위해 클라이언트에서 서버 API로 API 호출 수행</span><span class="sxs-lookup"><span data-stu-id="17bea-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="17bea-546">클라이언트에서 서버 API로 API 호출을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="17bea-547">서버에서 타사 API 리소스에 대한 액세스 토큰을 검색하고 필요한 호출이 무엇이든 해당 호출을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="17bea-548">이 방법을 사용하려면 타사 API를 호출하기 위해 서버를 통한 추가 네트워크 홉이 필요하지만, 궁극적으로 다음과 같은 더 안전한 환경이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="17bea-549">서버는 새로 고침 토큰을 저장하고 앱이 타사 리소스에 대한 액세스 권한을 잃지 않도록 보장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="17bea-550">앱은 더 중요한 권한을 포함할 수 있는 서버의 액세스 토큰을 누출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="17bea-551">OIDC (Open ID Connect) v2.0 끝점 사용</span><span class="sxs-lookup"><span data-stu-id="17bea-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="17bea-552">인증 라이브러리 및 Blazor 템플릿은 OIDC (OPEN ID Connect) v1.0 끝점을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="17bea-553">V2.0 끝점을 사용 하려면 JWT 전달자 옵션을 구성 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="17bea-554">다음 예제에서는 속성에 세그먼트를 추가 하 여 v 2.0에 대해 AAD를 구성 합니다 `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> .</span><span class="sxs-lookup"><span data-stu-id="17bea-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="17bea-555">또는 앱 설정 (*appsettings*) 파일에서 설정을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="17bea-556">권한에 대 한 세그먼트의 추적가 비 AAD 공급자와 같은 앱의 OIDC 공급자에 적합 하지 않은 경우 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="17bea-557"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>또는 앱 설정 파일 (*appsettings*)에서 키를 사용 하 여 속성을 설정 하거나 설정 합니다 `Authority` .</span><span class="sxs-lookup"><span data-stu-id="17bea-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="17bea-558">V2.0 끝점에 대 한 ID 토큰의 클레임 목록이 변경 됩니다.</span><span class="sxs-lookup"><span data-stu-id="17bea-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="17bea-559">자세한 내용은 [Microsoft identity platform (v2.0)로 업데이트 하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="17bea-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
