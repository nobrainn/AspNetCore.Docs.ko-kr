---
title: BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e8b1a1f86becb1e9f0affe14a667253bd0ec16bf
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153664"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

이 문서에서는 Blazor 인증을 위해 [AAD (AZURE ACTIVE DIRECTORY) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만드는 방법을 설명 합니다.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C에 앱 등록 및 솔루션 만들기

### <a name="create-a-tenant"></a>테넌트 만들기

[자습서: Azure Active Directory B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant) 의 지침에 따라 AAD B2C 테 넌 트를 만들고 다음 정보를 기록 합니다.

* AAD B2C 인스턴스 (예: `https://contoso.b2clogin.com/` 후행 슬래시를 포함 하는)
* AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` )

### <a name="register-a-server-api-app"></a>서버 API 앱 등록

[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory**앱 등록 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다  >  **App registrations** .

1. **새 등록**을 선택합니다.
1. 앱에 대 한 **이름** (예: ** Blazor Server AAD B2C**)을 제공 합니다.
1. **지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.** (다중 테 넌 트)이 환경에 적합 합니다.
1. *서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.
1. **Permissions**  >  **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 되어 있는지 확인 합니다.
1. **등록**을 선택합니다.

**API 노출**:

1. **범위 추가를**선택 합니다.
1. **저장하고 계속**을 선택합니다.
1. **범위 이름을** 제공 합니다 (예: `API.Access` ).
1. **관리자 동의 표시 이름** (예:)을 제공 `Access API` 합니다.
1. **관리자 동의 설명** (예:)을 제공 `Allows the app to access server app API endpoints.` 합니다.
1. **상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.
1. **범위 추가**를 선택 합니다.

다음 정보를 기록 합니다.

* *서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )
* 앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 제공한 사용자 지정 값)
* 디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222` )
* *서버 API 앱* 앱 ID URI (예:, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` Azure Portal 클라이언트 ID에 대 한 값을 기본값으로 사용할 수 있음)
* 기본 범위 (예: `API.Access` )

### <a name="register-a-client-app"></a>클라이언트 앱 등록

[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory**앱 등록 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다  >  **App registrations** .

1. **새 등록**을 선택합니다.
1. 응용 프로그램의 **이름** (예: ** Blazor 클라이언트 AAD B2C**)을 제공 합니다.
1. **지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.** (다중 테 넌 트)이 환경에 적합 합니다.
1. **리디렉션 uri** 드롭다운을 **웹**으로 유지 하 고의 리디렉션 uri를 제공 `https://localhost:5001/authentication/login-callback` 합니다.
1. **Permissions**  >  **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 되어 있는지 확인 합니다.
1. **등록**을 선택합니다.

**인증**  >  **플랫폼 구성**  >  **웹**에서:

1. 의 **리디렉션 URI** 가 있는지 확인 `https://localhost:5001/authentication/login-callback` 합니다.
1. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.
1. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.
1. **저장** 단추를 선택합니다.

**API 사용 권한**:

1. 앱에 사용자 **Microsoft Graph**있는지 확인  >  **합니다. 읽기** 권한이 필요 합니다.
1. **권한 추가를** 선택 하 고 **내 api**를 추가 합니다.
1. **이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: ** Blazor server AAD B2C**).
1. **API** 목록을 엽니다.
1. API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access` ).
1. **모든 권한**을 선택합니다.
1. **{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다. **예**를 선택하여 확인합니다.

**홈**  >  **Azure AD B2C**  >  **사용자 흐름**:

[가입 및 로그인 사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows)

최소한 **응용 프로그램 클레임**  >  **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.

다음 정보를 기록 합니다.

* *클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예: `33333333-3333-3333-3333-333333333333` ).
* 앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>앱 만들기

다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

> [!NOTE]
> 옵션에 앱 ID URI를 전달 `app-id-uri` 하지만 클라이언트 앱에 구성 변경이 필요할 수도 있습니다 .이에 대 한 자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명 되어 있습니다.

## <a name="server-app-configuration"></a>서버 앱 구성

*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 지원은에서 제공 됩니다 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` .

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>인증 서비스 지원

`AddAuthentication`메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다. `AddAzureADB2CBearer`메서드는 Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`그리고 `UseAuthorization` 다음을 확인 합니다.

* 앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.
* 적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>사용자. Identity 이름의

기본적으로는 `User.Identity.Name` 채워지지 않습니다.

클레임 형식에서 값을 받도록 앱을 구성 하려면 `name` 에서 [NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) 를 구성 합니다 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` .

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>앱 설정

*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

예제:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast 컨트롤러

WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 되는 특성을 사용 하 여 보호 된 API를 노출 합니다 `[Authorize]` . 다음을 이해 하는 것이 **중요** 합니다.

* 이 api `[Authorize]` 컨트롤러의 특성은이 api를 무단 액세스 로부터 보호 하는 유일한 방법입니다.
* `[Authorize]`Weasemboma 앱에서 사용 되는 특성은 Blazor 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*

### <a name="authentication-package"></a>인증 패키지

개별 B2C 계정 ()을 사용 하도록 앱을 만들면 `IndividualB2C` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` . 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

`{VERSION}`위의 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` 문서에 표시 된 패키지의 버전으로 바꿉니다 <xref:blazor/get-started> .

`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.

### <a name="authentication-service-support"></a>인증 서비스 지원

`HttpClient`서버 프로젝트에 대 한 요청을 만들 때 액세스 토큰을 포함 하는 인스턴스에 대 한 지원이 추가 됩니다.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` . 이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 Identity 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.

구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

예제:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a>액세스 토큰 범위

기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.

* 로그인 요청에 기본적으로 포함 됩니다.
* 인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.

모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다. 필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요. 예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 스키마 및 호스트 없이 범위 URI를 제공 합니다.
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행 합니다. Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:security/blazor/webassembly/additional-scenarios>
* [보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
