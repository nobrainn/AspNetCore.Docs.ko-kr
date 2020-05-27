`FetchData`구성 요소는 다음을 수행 하는 방법을 보여 줍니다.

* 액세스 토큰을 프로 비전 합니다.
* 액세스 토큰을 사용 하 여 *서버* 앱에서 보호 된 리소스 API를 호출 합니다.

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)지시문은이 구성 요소를 방문 하기 위해 사용자에 게 권한이 부여 되어야 한다는 Blazor Weasembomauthorization 시스템을 나타냅니다. *클라이언트* 앱에 특성이 있으면 서버에 있는 API가 적절 한 자격 증명 없이 호출 되는 것을 방지할 수 없습니다. 또한 *서버* 앱은 `[Authorize]` 적절 한 끝점에서를 사용 하 여 해당 끝점을 제대로 보호 해야 합니다.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>API를 호출 하기 위해 요청에 추가할 수 있는 액세스 토큰 요청을 처리 합니다. 토큰이 캐시 되거나 서비스에서 사용자 상호 작용 없이 새 액세스 토큰을 프로 비전 할 수 있으면 토큰 요청이 성공 합니다. 그렇지 않으면 토큰 요청이 실패 하 고이 실패 하 고가 try-catch <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 문에서 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) catch 됩니다.

요청에 포함할 실제 토큰을 가져오기 위해 앱은 [TryGetToken (out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)를 호출 하 여 요청이 성공 했는지 확인 해야 합니다.

요청에 성공 하면 토큰 변수가 액세스 토큰으로 채워집니다. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>토큰의 속성은 요청 헤더에 포함할 리터럴 문자열을 노출 합니다 `Authorization` .

사용자 상호 작용 없이 토큰을 프로 비전 할 수 없기 때문에 요청이 실패 한 경우 토큰 결과에 리디렉션 URL이 포함 됩니다. 이 URL로 이동 하면 사용자가 로그인 페이지로 이동 하 여 인증에 성공한 후에 현재 페이지로 돌아갑니다.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
