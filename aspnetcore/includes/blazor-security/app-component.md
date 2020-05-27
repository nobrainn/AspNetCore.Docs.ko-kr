`App`구성 요소 (Blazor)는 다음의 구성 요소와 유사*합니다.* `App`

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>구성 요소는 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 응용 프로그램의 나머지 부분에 대 한 공개를 관리 합니다.
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>구성 요소는 현재 사용자에 게 지정 된 페이지에 액세스할 수 있는 권한이 있는지, 그렇지 않으면 구성 요소를 렌더링 하도록 합니다 `RedirectToLogin` .
* `RedirectToLogin`구성 요소는 로그인 페이지에 대 한 권한 없는 사용자의 리디렉션을 관리 합니다.

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
