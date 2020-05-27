---
제목: ' ASP.NET Core Blazor 서버 추가 보안 시나리오 ' 작성자: 설명: ' Blazor 추가 보안 시나리오를 위해 서버를 구성 하는 방법을 알아봅니다. '
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor 서버 추가 보안 시나리오

작성자: [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>서버 앱에 토큰 전달 Blazor

서버 앱의 구성 요소 외부에서 사용할 수 있는 토큰은 Razor Blazor 이 섹션에 설명 된 방법으로 구성 요소에 전달 될 수 있습니다. 전체 예제를 비롯 한 샘플 코드는 `Startup.ConfigureServices` [서버 쪽 Blazor 응용 프로그램에 토큰 전달](https://github.com/javiercn/blazor-server-aad-sample)을 참조 하세요.

Blazor일반 페이지 또는 MVC 앱에서와 같이 서버 앱을 인증 Razor 합니다. 토큰을 프로 비전 하 여 인증 쿠키에 저장 합니다. 예를 들어:

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

액세스 및 새로 고침 토큰을 사용 하 여 초기 앱 상태를 전달 하는 클래스를 정의 합니다.

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

앱 내 **scoped** 에서 사용 하 여 Blazor [DI (종속성 주입)](xref:blazor/dependency-injection)에서 토큰을 확인할 수 있는 범위 토큰 공급자 서비스를 정의 합니다.

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

에서 `Startup.ConfigureServices` 다음에 대 한 서비스를 추가 합니다.

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host cshtml* 파일에서 및 인스턴스를 만들고 `InitialApplicationState` 앱에 매개 변수로 전달 합니다.

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

`App`구성 요소 (*응용 프로그램 razor*)에서 서비스를 확인 하 고 매개 변수에서 데이터를 사용 하 여 초기화 합니다.

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

보안 API 요청을 만드는 서비스에서 토큰 공급자를 삽입 하 고 API를 호출 하는 토큰을 검색 합니다.

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>OIDC (Open ID Connect) v2.0 끝점 사용

인증 라이브러리 및 Blazor 템플릿은 OIDC (OPEN ID Connect) v1.0 끝점을 사용 합니다. V2.0 끝점을 사용 하려면에서 옵션을 구성 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> .

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

또는 앱 설정 (*appsettings*) 파일에서 설정을 수행할 수 있습니다.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

권한에 대 한 세그먼트의 추적가 비 AAD 공급자와 같은 앱의 OIDC 공급자에 적합 하지 않은 경우 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 속성을 직접 설정 합니다. <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>또는 앱 설정 파일에서 키를 사용 하 여 속성을 설정 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> .

### <a name="code-changes"></a>코드 변경 내용

* V2.0 끝점에 대 한 ID 토큰의 클레임 목록이 변경 됩니다. 자세한 내용은 [Microsoft identity platform (v2.0)로 업데이트 하는 이유](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) 를 참조 하세요. Azure 설명서를 참조 하세요.
* V2.0 끝점에 대 한 범위 Uri에 리소스가 지정 되므로 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 에서 속성 설정을 제거 합니다 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> .

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

OIDC 공급자 앱 등록 설명에서 사용할 앱 ID URI를 찾을 수 있습니다.
