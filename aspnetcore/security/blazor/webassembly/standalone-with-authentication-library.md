---
제목: ' Blazor 인증 라이브러리를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱을 보호 합니다. ' 작성자: 설명: monikerRange: ms.:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Blazor인증 라이브러리를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

*AAD (Azure Active Directory) 및 Azure Active Directory B2C (AAD B2C)의 경우이 항목의 지침을 따르세요. 이 목차 노드의 AAD 및 AAD B2C 항목을 참조 하세요.*

Blazor라이브러리를 사용 하는 weasembom독립 실행형 앱을 만들려면 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 명령 셸에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au Individual
```

출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

Visual Studio에서 [ Blazor weasembomapp을 만듭니다](xref:blazor/get-started). **사용자 계정을 앱에 저장** 옵션을 사용 하 여 **개별 사용자 계정** 에 대 한 **인증** 을 설정 합니다.

## <a name="authentication-package"></a>인증 패키지

개별 사용자 계정을 사용 하도록 앱을 만들 때 앱은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 앱의 프로젝트 파일에서 패키지에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` . 이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

독립형 앱에 대 한 인증 지원은 OIDC (Open ID Connect)를 사용 하 여 제공 됩니다. `AddOidcAuthentication`메서드는 OIDC를 사용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다. 앱을 구성 하는 데 필요한 값은 OIDC 규격 IP에서 가져올 수 있습니다. 앱을 등록 하면 일반적으로 온라인 포털에서 발생 하는 값을 가져옵니다.

## <a name="access-token-scopes"></a>액세스 토큰 범위

BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다. 액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 토큰 범위에 범위를 추가 합니다 `OidcProviderOptions` .

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

구성 `LoginDisplay` 요소 (*Shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*shared/mainlayout. razor*)에서 렌더링 되 고 다음 동작을 관리 합니다.

* 인증 된 사용자의 경우:
  * 현재 사용자 이름을 표시 합니다.
  * 앱에서 로그 아웃할 수 있는 단추를 제공 합니다.
* 익명 사용자의 경우은 로그인 할 수 있는 옵션을 제공 합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/blazor/webassembly/additional-scenarios>
* [보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
