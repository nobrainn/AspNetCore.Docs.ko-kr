구성 `FetchData` 요소는 다음 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과

* 액세스 토큰을 프로비전합니다.
* 액세스 토큰을 사용하여 *Server* 앱에서 보호된 리소스 API를 호출합니다.

이 `@attribute [Authorize]` 지시문은 Blazor WebAssembly 권한 부여 시스템에 사용자가 이 구성 요소를 방문하려면 권한이 있어야 함을 나타냅니다. *클라이언트* 앱에 특성이 있다고 해서 서버의 API가 적절한 자격 증명 없이 호출되는 것을 방지할 수 없습니다. 또한 *서버* 앱은 `[Authorize]` 적절한 끝점에서 서버를 올바르게 보호해야 합니다.

`AuthenticationService.RequestAccessToken();`는 API 호출 요청에 추가할 수 있는 액세스 토큰을 요청하는 것을 처리합니다. 토큰이 캐시되어 있거나 서비스가 사용자 상호 작용 없이 새 액세스 토큰을 프로비전할 수 있는 경우 토큰 요청이 성공합니다. 그렇지 않으면 토큰 요청이 실패합니다.

요청에 포함할 실제 토큰을 얻으려면 앱에서 요청이 호출하여 `tokenResult.TryGetToken(out var token)`성공여부를 확인해야 합니다. 

요청이 성공하면 토큰 변수가 액세스 토큰으로 채워집니다. 토큰의 속성은 `Value` 요청 헤더에 포함할 리터럴 문자열을 노출합니다. `Authorization`

사용자 상호 작용 없이 토큰을 프로비전할 수 없기 때문에 요청이 실패한 경우 토큰 결과에 리디렉션 URL이 포함됩니다. 이 URL로 이동하면 인증에 성공한 후 로그인 페이지로 이동하여 현재 페이지로 돌아갑니다.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

자세한 내용은 [인증 작업 전에 앱 상태 저장을](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)참조하십시오.
