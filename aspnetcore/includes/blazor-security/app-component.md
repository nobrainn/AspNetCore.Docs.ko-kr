구성 요소 (Blazor)는 다음의 `App` 구성 요소와*유사 합니다.* `App`

* 구성 `CascadingAuthenticationState` 요소는 응용 프로그램 `AuthenticationState` 의 나머지 부분에 대 한 공개를 관리 합니다.
* 구성 `AuthorizeRouteView` 요소는 현재 사용자에 게 지정 된 페이지에 액세스할 수 있는 권한이 있는지, 그렇지 않으면 `RedirectToLogin` 구성 요소를 렌더링 하도록 합니다.
* 구성 `RedirectToLogin` 요소는 로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
