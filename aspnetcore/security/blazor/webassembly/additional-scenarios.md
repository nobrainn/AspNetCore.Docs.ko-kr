---
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Blazor추가 보안 시나리오 ASP.NET Core

작성자: [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="attach-tokens-to-outgoing-requests"></a>나가는 요청에 토큰 연결

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>서비스를와 함께 사용 <xref:System.Net.Http.HttpClient> 하 여 액세스 토큰을 보내는 요청에 연결할 수 있습니다. 토큰은 기존 서비스를 사용 하 여 획득 됩니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> . 토큰을 가져올 수 없는 경우 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 이 throw 됩니다. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>에는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> 사용자가 id 공급자로 이동 하 여 새 토큰을 획득 하는 데 사용할 수 있는 메서드가 있습니다. 는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 메서드를 사용 하 여 권한 있는 url, 범위 및 반환 URL을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> .

다음 예제에서는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> `Program.Main` (*Program.cs*)에서를 구성 합니다.

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

편의상 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> 앱 기준 주소를 권한 있는 URL로 미리 구성 된가 포함 되어 있습니다. 이제 인증 사용 Blazor weasembomomtemplate이 <xref:System.Net.Http.IHttpClientFactory> 서버 API 프로젝트에서를 사용 하 여를 설정 합니다 <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> .

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

앞의 예제에서 클라이언트를 만든 경우 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> 서버 프로젝트에 요청을 수행할 때 액세스 토큰이 포함 된 인스턴스가 제공 됩니다.

그런 다음 구성 된를 사용 하 여 <xref:System.Net.Http.HttpClient> 간단한 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 통해 권한 있는 요청을 수행할 수 있습니다.

`FetchData` 구성 요소(*Pages/FetchData.razor*):

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

## <a name="typed-httpclient"></a>형식화된 HttpClient

단일 클래스 내에서 모든 HTTP 및 토큰 획득 문제를 처리 하는 형식화 된 클라이언트를 정의할 수 있습니다.

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData` 구성 요소(*Pages/FetchData.razor*):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>HttpClient 처리기 구성

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>아웃 바운드 HTTP 요청에 대해를 사용 하 여 처리기를 추가로 구성할 수 있습니다.

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청

BlazorWeasembomapp이 일반적으로 보안 기본값을 사용 하는 경우 <xref:System.Net.Http.HttpClient> 앱은 명명 된를 구성 하 여 인증 되지 않은 웹 API 요청 또는 인증 되지 않은 <xref:System.Net.Http.HttpClient>

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

위의 등록은 기존 보안 기본 등록에 추가 됩니다 <xref:System.Net.Http.HttpClient> .

구성 요소는 <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> 인증 되지 않거나 권한이 없는 요청을 만들기 위해에서를 만듭니다.

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
> `WeatherForecastNoAuthenticationController`이전 예제에 대 한 서버 API의 컨트롤러는 특성으로 표시 되지 않습니다 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .

## <a name="request-additional-access-tokens"></a>추가 액세스 토큰 요청

액세스 토큰은를 호출 하 여 수동으로 가져올 수 있습니다 `IAccessTokenProvider.RequestAccessToken` .

다음 예제에서는 앱에서 사용자 데이터를 읽고 메일을 보내기 위해 AAD (추가 Azure Active Directory) Microsoft Graph API 범위가 필요 합니다. Azure AAD 포털에서 Microsoft Graph API 사용 권한을 추가한 후 클라이언트 앱에서 추가 범위가 구성 됩니다.

`Program.Main`(*Program.cs*):

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

`IAccessTokenProvider.RequestToken`메서드는 앱이 지정 된 범위 집합을 사용 하 여 액세스 토큰을 프로 비전 할 수 있도록 하는 오버 로드를 제공 합니다.

Razor구성 요소에서:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>전용인

* `true`사용할를 `token` 사용 합니다.
* `false`토큰이 검색 되지 않으면입니다.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>Fetch API 요청 옵션이 포함된 HttpClient 및 HttpRequestMessage

Weasembomambomapp에서 제대로 실행 되는 경우 Blazor [httpclient](xref:fundamentals/http-requests) 를 <xref:System.Net.Http.HttpRequestMessage> 사용 하 여 요청을 사용자 지정할 수 있습니다. 예를 들어 HTTP 메서드 및 요청 헤더를 지정할 수 있습니다. 다음 구성 요소는 `POST` 서버에서 To Do LIST API 끝점에 대 한 요청을 수행 하 고 응답 본문을 표시 합니다.

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

.NET WebAssembly의 <xref:System.Net.Http.HttpClient> 구현은 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)를 사용합니다. Fetch를 사용하면 여러 [요청 관련 옵션](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)을 구성할 수 있습니다. 

HTTP 페치 요청 옵션은 다음 표에 표시된 <xref:System.Net.Http.HttpRequestMessage> 확장 메서드로 구성할 수 있습니다.

| 확장 메서드 | Fetch 요청 속성 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [캐시](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [무결성](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

보다 일반적인 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 확장 메서드를 사용하여 추가 옵션을 설정할 수 있습니다.
 
HTTP 응답은 일반적으로 응답 콘텐츠에서 동기화 읽기를 지원할 수 있도록 Blazor WebAssembly 앱에서 버퍼링됩니다. 응답 스트리밍을 지원하도록 설정하려면 요청에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 확장 메서드를 사용합니다.

원본 간 요청에 자격 증명을 포함하려면 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 확장 메서드를 사용합니다.

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.

CORS 요청에 대한 자격 증명(권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용해야 합니다.

다음 정책에는 해당 구성이 포함되어 있습니다.

* 요청 원본(`http://localhost:5000`, `https://localhost:5001`).
* 임의 메서드(동사)
* `Content-Type` 및 `Authorization` 헤더. 사용자 지정 헤더(예: `x-custom-header`)를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열합니다.
* 클라이언트 쪽 JavaScript 코드에 의해 설정된 자격 증명(`credentials` 속성이 `include`로 설정됨).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소(*Components/HTTPRequestTester.razor*)를 참조하세요.

## <a name="handle-token-request-errors"></a>토큰 요청 오류 처리

SPA (단일 페이지 응용 프로그램)가 OIDC (Open ID Connect)를 사용 하 여 사용자를 인증 하는 경우 인증 상태는 해당 Identity 자격 증명을 제공 하는 사용자의 결과로 설정 된 세션 쿠키의 형태로 spa 및 공급자 (IP) 내에서 로컬로 유지 관리 됩니다.

일반적으로 사용자에 대 한 IP가 내보내는 토큰은 짧은 시간 동안 일반적으로 1 시간 동안 유효 하므로 클라이언트 앱이 정기적으로 새 토큰을 가져와야 합니다. 그렇지 않으면 부여 된 토큰이 만료 된 후 사용자가 로그 아웃 됩니다. 대부분의 경우 OIDC 클라이언트는 사용자가 IP 내에 유지 되는 인증 상태 또는 "세션"으로 인해 다시 인증 하도록 요구 하지 않고 새 토큰을 프로 비전 할 수 있습니다.

사용자가 사용자 개입 없이 토큰을 가져올 수 없는 경우도 있습니다. 예를 들어 사용자가 IP에서 명시적으로 로그 아웃 하는 이유가 있습니다. 이 시나리오는 사용자가 방문 하 여 로그 아웃 하는 경우에 발생 `https://login.microsoftonline.com` 합니다. 이러한 시나리오에서 앱은 사용자가 로그 아웃 한 즉시 인식 하지 못합니다. 클라이언트에서 보유 하는 모든 토큰은 더 이상 유효 하지 않을 수 있습니다. 또한 클라이언트는 현재 토큰이 만료 된 후 사용자 상호 작용 없이 새 토큰을 프로 비전 할 수 없습니다.

이러한 시나리오는 토큰 기반 인증에 국한 되지 않습니다. 이러한 특성은 SPAs 특성의 일부입니다. 쿠키를 사용 하는 SPA는 인증 쿠키를 제거 하는 경우에도 서버 API를 호출 하지 못합니다.

앱에서 보호 된 리소스에 대 한 API 호출을 수행 하는 경우 다음 사항을 알고 있어야 합니다.

* 새 액세스 토큰을 프로 비전 하 여 API를 호출 하려면 사용자가 다시 인증 해야 할 수 있습니다.
* 클라이언트에 유효한 토큰이 있는 경우에도 토큰을 사용자가 해지 했기 때문에 서버에 대 한 호출이 실패할 수 있습니다.

앱에서 토큰을 요청 하는 경우 다음과 같은 두 가지 결과를 사용할 수 있습니다.

* 요청이 성공 하 고 앱에 유효한 토큰이 있습니다.
* 요청이 실패 하 고 앱이 사용자를 다시 인증 하 여 새 토큰을 가져와야 합니다.

토큰 요청이 실패 하는 경우 리디렉션을 수행 하기 전에 현재 상태를 저장할 것인지 여부를 결정 해야 합니다. 복잡성 수준이 증가 하는 방법에는 여러 가지가 있습니다.

* 세션 저장소에 현재 페이지 상태를 저장 합니다. [Oninitializedasync 수명 주기 이벤트](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) 중에 상태를 복원할 수 있는지 확인 한 다음 계속 합니다.
* 쿼리 문자열 매개 변수를 추가 하 고이 매개 변수를 사용 하 여 이전에 저장 된 상태를 다시 하이드레이션 하며 나중 하는 데 필요한 것으로 앱에 신호를 보낼 수 있습니다.
* 다른 항목과 충돌을 발생 시 키 지 않고 세션 저장소에 데이터를 저장 하는 고유 식별자를 사용 하 여 쿼리 문자열 매개 변수를 추가 합니다.

아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 로그인 페이지로 리디렉션하기 전에 상태를 유지 합니다.
* 쿼리 문자열 매개 변수를 사용 하 여 인증 이후 이전 상태를 복구 합니다.

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

## <a name="save-app-state-before-an-authentication-operation"></a>인증 작업 전에 앱 상태 저장

인증 작업을 수행 하는 동안 브라우저가 IP로 리디렉션되도록 응용 프로그램 상태를 저장 하려는 경우가 있습니다. 상태 컨테이너와 같은 항목을 사용 하 고 인증에 성공 하 고 나면 상태를 복원 하려는 경우를 들 수 있습니다. 사용자 지정 인증 상태 개체를 사용 하 여 앱 특정 상태 또는 참조를 보존 하 고 인증 작업이 성공적으로 완료 되 면 해당 상태를 복원할 수 있습니다.

`Authentication`구성 요소 (*페이지/인증. razor*):

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

## <a name="customize-app-routes"></a>앱 경로 사용자 지정

기본적으로 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 라이브러리는 다음 표에 나와 있는 경로를 사용 하 여 서로 다른 인증 상태를 나타냅니다.

| 경로                            | 목적 |
| ---
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---------------- | ---제목: ' ASP.NET Core Blazor weasembmbambaoma 추가 보안 시나리오 ' 작성자: 설명: ' Blazor 추가 보안 시나리오를 위해 weasembmbomommbmbommbmbmboms
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---- | | `authentication/login`           | 로그인 작업을 트리거합니다. | | `authentication/login-callback`  | 로그인 작업의 결과를 처리 합니다. | | `authentication/login-failed`    | 어떤 이유로 인해 로그인 작업이 실패 하는 경우 오류 메시지를 표시 합니다. | | `authentication/logout`          | 로그 아웃 작업을 트리거합니다. | | `authentication/logout-callback` | 로그 아웃 작업의 결과를 처리 합니다. | | `authentication/logout-failed`   | 어떤 이유로 인해 로그 아웃 작업이 실패 하는 경우 오류 메시지를 표시 합니다. | | `authentication/logged-out`      | 사용자가 성공적으로 로그 아웃 했음을 나타냅니다. | | `authentication/profile`         | 사용자 프로필을 편집 하는 작업을 트리거합니다. | | `authentication/register`        | 새 사용자를 등록 하는 작업을 트리거합니다. |

위의 표에 표시 된 경로는를 통해 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . 사용자 지정 경로를 제공 하는 옵션을 설정 하는 경우 앱에 각 경로를 처리 하는 경로가 있는지 확인 합니다.

다음 예제에서 모든 경로에는 접두사가 붙습니다 `/security` .

`Authentication`구성 요소 (*페이지/인증. razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

요구 사항이 완전히 다른 경로에 대해를 호출 하는 경우 앞에서 설명한 대로 경로를 설정 하 고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 명시적인 동작 매개 변수를 사용 하 여을 렌더링 합니다.

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

이렇게 선택 하면 UI를 다른 페이지로 나눌 수 있습니다.

## <a name="customize-the-authentication-user-interface"></a>인증 사용자 인터페이스 사용자 지정

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>에는 각 인증 상태에 대 한 기본 UI 조각 집합이 포함 되어 있습니다. 사용자 지정를 전달 하 여 각 상태를 사용자 지정할 수 있습니다 <xref:Microsoft.AspNetCore.Components.RenderFragment> . 초기 로그인 프로세스 중에 표시 되는 텍스트를 사용자 지정 하려면에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 다음과 같이 변경할 수 있습니다.

`Authentication`구성 요소 (*페이지/인증. razor*):

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

에는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 다음 표에 표시 된 인증 경로 별로 사용할 수 있는 하나의 조각이 있습니다.

| 경로                            | 조각                |
| ---
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---------------- | ---제목: ' ASP.NET Core Blazor weasembmbambaoma 추가 보안 시나리오 ' 작성자: 설명: ' Blazor 추가 보안 시나리오를 위해 weasembmbomommbmbommbmbmboms
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

-
제목: ' ASP.NET Core Blazor Weasembmbmbsembambambambambambambambmbsembmbsembmbommbmbmbmbommbmbmbomommbmb Blazor
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a>사용자 사용자 지정

앱에 바인딩된 사용자를 사용자 지정할 수 있습니다. 다음 예에서는 인증 된 모든 사용자가 `amr` 각 사용자의 인증 방법에 대 한 클레임을 받습니다.

클래스를 확장 하는 클래스를 만듭니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

다음을 확장 하는 팩터리를 만듭니다 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> .

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

`CustomAccountFactory`사용 중인 인증 공급자에 대 한를 등록 합니다. 유효한 등록은 다음과 같습니다. 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

## <a name="support-prerendering-with-authentication"></a>인증을 사용한 미리 렌더링 지원

호스트된 Blazor WebAssembly 앱 토픽 중 하나의 지침을 따른 후에는 다음 지침에 따라 다음과 같은 동작을 수행하는 앱을 만듭니다.

* 인증이 필요하지 않은 경로를 미리 렌더링합니다.
* 인증이 필요한 경로를 미리 렌더링하지 않습니다.

클라이언트 앱의 `Program` 클래스(*Program.cs*)에서 공통 서비스 등록을 별도의 메서드(예: `ConfigureCommonServices`)로 팩터링합니다.

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

서버 앱의 `Startup.ConfigureServices`에서 다음과 같은 추가 서비스를 등록합니다.

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

서버 앱의 `Startup.Configure` 메서드에서 끝점을 대체 [합니다. 끝점을 사용 하는 MapFallbackToFile ("index .html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [ MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

서버 앱에서 *Pages* 폴더가 없으면 이 폴더를 만듭니다. 서버 앱의 *Pages* 폴더 안에 *_Host.cshtml* 페이지를 만듭니다. 클라이언트 앱의 *wwwroot/index.html* 파일 콘텐츠를 *Pages/_Host.cshtml* 파일에 붙여넣습니다. 다음과 같이 파일 콘텐츠를 업데이트합니다.

* `@page "_Host"`를 파일의 맨 위에 추가합니다.
* `<app>Loading...</app>` 태그를 다음으로 바꿉니다.

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>호스트된 앱 및 타사 로그인 공급자에 대한 옵션

타사 공급자를 사용하여 호스트된 Blazor WebAssembly 앱을 인증하고 권한을 부여하는 경우 사용자를 인증하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 시나리오에 따라 선택하는 옵션이 달라집니다.

자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>보호된 타사 API만 호출하도록 사용자 인증

클라이언트 쪽 OAuth 흐름을 사용하여 사용자를 타사 API 공급자에 대해 인증합니다.

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 이 시나리오에서는

* 앱을 호스트하는 서버에서 역할을 재생하지 않습니다.
* 서버의 API는 보호할 수 없습니다.
* 앱은 보호된 타사 API만 호출할 수 있습니다.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>타사 공급자를 사용하여 사용자를 인증하고 호스트 서버 및 타사에서 보호된 API 호출

Identity타사 로그인 공급자를 사용 하 여를 구성 합니다. 타사 API 액세스에 필요한 토큰을 가져와 저장합니다.

사용자가 로그인 할 때는 Identity 인증 프로세스의 일부로 액세스 및 새로 고침 토큰을 수집 합니다. 이때 타사 API에 대한 API 호출을 수행하는 데 사용할 수 있는 몇 가지 방법이 있습니다.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>서버 액세스 토큰을 사용하여 타사 액세스 토큰 검색

서버에서 생성된 액세스 토큰을 사용하여 서버 API 엔드포인트에서 타사 액세스 토큰을 검색합니다. 여기에서 타사 액세스 토큰을 사용 하 여 클라이언트에서 직접 타사 API 리소스를 호출 Identity 합니다.

이 방법은 사용하지 않는 것이 좋습니다. 이 방법을 사용하려면 퍼블릭 클라이언트에 대해 생성된 것처럼 타사 액세스 토큰을 처리해야 합니다. OAuth 맥락에서, 퍼블릭 앱은 암호를 안전하게 저장하는 데 신뢰할 수 없으므로 클라이언트 암호를 갖지 않으며, 액세스 토큰은 기밀 클라이언트에 대해 생성됩니다. 기밀 클라이언트는 클라이언트 암호를 포함하는 클라이언트이며 비밀을 안전하게 저장할 수 있는 것으로 간주됩니다.

* 타사 액세스 토큰에는 타사에서 더 신뢰할 수 있는 클라이언트에 대한 토큰을 내보낸 사실을 기반으로 중요한 작업을 수행하기 위한 추가 범위가 부여될 수 있습니다.
* 마찬가지로, 신뢰할 수 없는 클라이언트에 대해서는 새로 고침 토큰을 발급하지 않아야 합니다. 발급할 경우, 다른 제한이 적용되지 않는 한 클라이언트에 무제한 액세스 권한이 제공됩니다.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>타사 API를 호출하기 위해 클라이언트에서 서버 API로 API 호출 수행

클라이언트에서 서버 API로 API 호출을 수행합니다. 서버에서 타사 API 리소스에 대한 액세스 토큰을 검색하고 필요한 호출이 무엇이든 해당 호출을 실행합니다.

이 방법을 사용하려면 타사 API를 호출하기 위해 서버를 통한 추가 네트워크 홉이 필요하지만, 궁극적으로 다음과 같은 더 안전한 환경이 생성됩니다.

* 서버는 새로 고침 토큰을 저장하고 앱이 타사 리소스에 대한 액세스 권한을 잃지 않도록 보장할 수 있습니다.
* 앱은 더 중요한 권한을 포함할 수 있는 서버의 액세스 토큰을 누출할 수 없습니다.

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>OIDC (Open ID Connect) v2.0 끝점 사용

인증 라이브러리 및 Blazor 템플릿은 OIDC (OPEN ID Connect) v1.0 끝점을 사용 합니다. V2.0 끝점을 사용 하려면 JWT 전달자 옵션을 구성 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> 합니다. 다음 예제에서는 속성에 세그먼트를 추가 하 여 v 2.0에 대해 AAD를 구성 합니다 `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> .

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

또는 앱 설정 (*appsettings*) 파일에서 설정을 수행할 수 있습니다.

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

권한에 대 한 세그먼트의 추적가 비 AAD 공급자와 같은 앱의 OIDC 공급자에 적합 하지 않은 경우 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정 합니다. <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>또는 앱 설정 파일 (*appsettings*)에서 키를 사용 하 여 속성을 설정 하거나 설정 합니다 `Authority` .

V2.0 끝점에 대 한 ID 토큰의 클레임 목록이 변경 됩니다. 자세한 내용은 [Microsoft identity platform (v2.0)로 업데이트 하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)를 참조 하세요.
