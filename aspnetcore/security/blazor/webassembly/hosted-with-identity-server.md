---
title: Blazor서버를 사용 하 여 ASP.NET Core weasemboman 호스팅된 앱 보안 Identity
author: guardrex
description: Blazor [IdentityServer](https://identityserver.io/) 백 엔드를 사용 하는 Visual Studio 내에서 인증을 사용 하 여 새 호스트 된 앱을 만들려면
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ade2d88c6a2d59e169c9019e871982a74ae46b33
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452319"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Blazor서버를 사용 하 여 ASP.NET Core weasemboman 호스팅된 앱 보안 Identity

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

이 문서에서는 Blazor [IdentityServer](https://identityserver.io/) 를 사용 하 여 사용자 및 API 호출을 인증 하는 새 호스팅된 앱을 만드는 방법을 설명 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 다음을 수행합니다.

1. 새 ** Blazor weasembomapp** 을 만듭니다. 자세한 내용은 <xref:blazor/get-started>를 참조하세요.
1. **새 Blazor 앱 만들기** 대화 상자의 **인증** 섹션에서 **변경** 을 선택 합니다.
1. **개별 사용자 계정** , **확인을**차례로 선택 합니다.
1. **고급** 섹션에서 **호스팅된 ASP.NET Core** 확인란을 선택 합니다.
1. **만들기** 단추를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 앱을 만들려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

---

## <a name="server-app-configuration"></a>서버 앱 구성

다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.

### <a name="startup-class"></a>시작 클래스

클래스에는 `Startup` 다음과 같은 추가 기능이 있습니다.

* `Startup.ConfigureServices`의 경우

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> IdentityServer 위에 기본 ASP.NET Core 규칙을 설정 하는 추가 도우미 메서드를 사용 합니다.

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 도우미 메서드를 사용 하 여 인증 합니다.

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`의 경우

  * IdentityServer 미들웨어는 OIDC (Open ID Connect) 끝점을 제공 합니다.

    ```csharp
    app.UseIdentityServer();
    ```

  * 인증 미들웨어는 요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 하는 작업을 담당 합니다.

    ```csharp
    app.UseAuthentication();
    ```

  * 권한 부여 미들웨어는 권한 부여 기능을 사용 합니다.

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>도우미 메서드는 ASP.NET Core 시나리오에 대 한 [IdentityServer](https://identityserver.io/) 를 구성 합니다. IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다. IdentityServer는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다. 따라서 유용한 시작 지점을 고려 하는 규칙 집합과 구성 옵션이 제공 됩니다. 인증을 변경 해야 하는 경우 앱의 요구 사항에 맞게 인증을 사용자 지정 하는 데 IdentityServer의 모든 기능을 사용할 수 있습니다.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다. 정책은 Identity URL 공간의 모든 하위 경로로 라우팅되는 모든 요청을 처리할 수 있도록 구성 됩니다 Identity `/Identity` . 는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 다른 모든 요청을 처리 합니다. 또한이 메서드는 다음과 같습니다.

* `{APPLICATION NAME}API`기본 범위인 IdentityServer를 사용 하 여 API 리소스를 등록 `{APPLICATION NAME}API` 합니다.
* 앱에 대해 IdentityServer에서 발급 한 토큰의 유효성을 검사 하도록 JWT 전달자 토큰 미들웨어를 구성 합니다.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

`WeatherForecastController`(*Controller/WeatherForecastController*)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 클래스에 적용 됩니다. 특성은 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한이 부여 되어야 함을 나타냅니다. 기본 권한 부여 정책은에 의해 설정 되는 기본 인증 체계를 사용 하도록 구성 됩니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . 도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 를 앱에 대 한 요청에 대 한 기본 처리기로 구성 합니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

`ApplicationDbContext`(*Data/ApplicationDbContext*)에서 <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> IdentityServer에 대 한 스키마를 포함 하도록를 확장 합니다. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.

데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 클래스 중 하나에서 상속 하 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 고 Identity 메서드를 호출 하 여 스키마를 포함 하도록 컨텍스트를 구성 합니다 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

`OidcConfigurationController`(*Controller/OidcConfigurationController*)에서 클라이언트 끝점은 oidc 매개 변수를 제공 하도록 프로 비전 됩니다.

### <a name="app-settings-files"></a>앱 설정 파일

프로젝트 루트의 앱 설정 파일 (*appsettings*)에서 `IdentityServer` 섹션은 구성 된 클라이언트 목록에 대해 설명 합니다. 다음 예제에는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다. 프로필은 구성 중인 앱 유형을 나타냅니다. 프로필은 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>클라이언트 앱 구성

### <a name="authentication-package"></a>인증 패키지

개별 사용자 계정 ()을 사용 하도록 앱을 만들 때 앱 `Individual` 은 앱의 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>API 권한 부여 지원

사용자 인증에 대 한 지원은 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 내에서 제공 하는 확장 메서드에 의해 서비스 컨테이너에 연결 됩니다. 이 메서드는 앱에서 기존 권한 부여 시스템과 상호 작용 하는 데 필요한 서비스를 설정 합니다.

```csharp
builder.Services.AddApiAuthorization();
```

기본적으로 앱에 대 한 구성은에서 규칙에 따라 로드 됩니다 `_configuration/{client-id}` . 규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정 됩니다. 옵션으로 오버 로드를 호출 하 여 별도의 끝점을 가리키도록이 URL을 변경할 수 있습니다.

### <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

구성 `LoginDisplay` 요소 (*Shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*shared/mainlayout. razor*)에서 렌더링 되 고 다음 동작을 관리 합니다.

* 인증 된 사용자의 경우:
  * 현재 사용자 이름을 표시 합니다.
  * ASP.NET Core의 사용자 프로필 페이지에 대 한 링크를 제공 Identity 합니다.
  * 앱에서 로그 아웃할 수 있는 단추를 제공 합니다.
* 익명 사용자의 경우:
  * 등록할 수 있는 옵션을 제공 합니다.
  * 로그인 할 수 있는 옵션을 제공 합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행 합니다. Visual Studio를 사용 하는 경우 다음 중 하나를 수행 합니다.

* 도구 모음에서 **시작 프로젝트** 드롭다운 목록을 *서버 API 앱* 으로 설정 하 고 **실행** 단추를 선택 합니다.
* **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.

## <a name="name-and-role-claim-with-api-authorization"></a>API 권한 부여를 사용 하 여 이름 및 역할 클레임

### <a name="custom-user-factory"></a>사용자 지정 사용자 팩터리

클라이언트 앱에서 사용자 지정 사용자 팩터리를 만듭니다. Identity서버는 단일 클레임에서 여러 역할을 JSON 배열로 보냅니다 `role` . 단일 역할은 클레임에서 문자열 값으로 전송 됩니다. 팩터리는 `role` 각 사용자의 역할에 대 한 개별 클레임을 만듭니다.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

클라이언트 앱에서 팩터리 `Program.Main` (*Program.cs*)를 등록 합니다.

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

서버 앱에서 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 작성기에 대해를 호출 하면 Identity 역할 관련 서비스가 추가 됩니다.

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Identity서버 구성

다음 방법 중 **하나**를 사용합니다.

* [API 권한 부여 옵션](#api-authorization-options)
* [프로필 서비스](#profile-service)

#### <a name="api-authorization-options"></a>API 권한 부여 옵션

서버 앱에서:

* Identity `name` 및 `role` 클레임을 ID 토큰 및 액세스 토큰에 추가 하도록 서버를 구성 합니다.
* JWT 토큰 처리기에서 역할에 대 한 기본 매핑을 방지 합니다.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>프로필 서비스

서버 앱에서 `ProfileService` 구현을 만듭니다.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

서버 앱에서 프로필 서비스를 등록 합니다 `Startup.ConfigureServices` .

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>권한 부여 메커니즘 사용

클라이언트 앱에서는이 시점에서 구성 요소 권한 부여 방법이 작동 합니다. 구성 요소의 권한 부여 메커니즘은 역할을 사용 하 여 사용자에 게 권한을 부여할 수 있습니다.

* [AuthorizeView 구성 요소](xref:security/blazor/index#authorizeview-component) (예: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` attribute 지시문](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (예: `@attribute [Authorize(Roles = "admin")]` )
* [절차적 논리](xref:security/blazor/index#procedural-logic) (예: `if (user.IsInRole("admin")) { ... }` )

  여러 역할 테스트가 지원 됩니다.

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`는 클라이언트 앱에서 사용자의 사용자 이름 (일반적으로 로그인 메일 주소)으로 채워집니다.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [Azure App Service 배포](xref:security/authentication/identity/spa#deploy-to-production)
* [Key Vault에서 인증서 가져오기 (Azure 설명서)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
