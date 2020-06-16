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
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776516"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="63f55-103">Blazor서버를 사용 하 여 ASP.NET Core weasemboman 호스팅된 앱 보안 Identity</span><span class="sxs-lookup"><span data-stu-id="63f55-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="63f55-104">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="63f55-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="63f55-105">이 문서에서는 Blazor [IdentityServer](https://identityserver.io/) 를 사용 하 여 사용자 및 API 호출을 인증 하는 새 호스팅된 앱을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63f55-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63f55-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="63f55-107">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-107">In Visual Studio:</span></span>

1. <span data-ttu-id="63f55-108">새 \*\* Blazor weasembomapp\*\* 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="63f55-109">자세한 내용은 <xref:blazor/get-started> 을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="63f55-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="63f55-110">**새 Blazor 앱 만들기** 대화 상자의 **인증** 섹션에서 **변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="63f55-111">**개별 사용자 계정** , **확인을**차례로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="63f55-112">**고급** 섹션에서 **호스팅된 ASP.NET Core** 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="63f55-113">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="63f55-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="63f55-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="63f55-115">명령 셸에서 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="63f55-116">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="63f55-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="63f55-117">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="63f55-118">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="63f55-118">Server app configuration</span></span>

<span data-ttu-id="63f55-119">다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="63f55-120">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="63f55-120">Startup class</span></span>

<span data-ttu-id="63f55-121">클래스에는 `Startup` 다음과 같은 추가 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="63f55-122">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="63f55-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="63f55-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="63f55-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="63f55-124">IdentityServer <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> IdentityServer 위에 기본 ASP.NET Core 규칙을 설정 하는 추가 도우미 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="63f55-125"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 도우미 메서드를 사용 하 여 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="63f55-126">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="63f55-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="63f55-127">IdentityServer 미들웨어는 OIDC (Open ID Connect) 끝점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="63f55-128">인증 미들웨어는 요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 하는 작업을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="63f55-129">권한 부여 미들웨어는 권한 부여 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="63f55-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="63f55-130">AddApiAuthorization</span></span>

<span data-ttu-id="63f55-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>도우미 메서드는 ASP.NET Core 시나리오에 대 한 [IdentityServer](https://identityserver.io/) 를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="63f55-132">IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="63f55-133">IdentityServer는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="63f55-134">따라서 유용한 시작 지점을 고려 하는 규칙 집합과 구성 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="63f55-135">인증을 변경 해야 하는 경우 앱의 요구 사항에 맞게 인증을 사용자 지정 하는 데 IdentityServer의 모든 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="63f55-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="63f55-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="63f55-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="63f55-138">정책은 Identity URL 공간의 모든 하위 경로로 라우팅되는 모든 요청을 처리할 수 있도록 구성 됩니다 Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="63f55-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="63f55-139">는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 다른 모든 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="63f55-140">또한이 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-140">Additionally, this method:</span></span>

* <span data-ttu-id="63f55-141">`{APPLICATION NAME}API`기본 범위인 IdentityServer를 사용 하 여 API 리소스를 등록 `{APPLICATION NAME}API` 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="63f55-142">앱에 대해 IdentityServer에서 발급 한 토큰의 유효성을 검사 하도록 JWT 전달자 토큰 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="63f55-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="63f55-143">WeatherForecastController</span></span>

<span data-ttu-id="63f55-144">`WeatherForecastController`(*Controller/WeatherForecastController*)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 클래스에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="63f55-145">특성은 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한이 부여 되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="63f55-146">기본 권한 부여 정책은에 의해 설정 되는 기본 인증 체계를 사용 하도록 구성 됩니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="63f55-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="63f55-147">도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 를 앱에 대 한 요청에 대 한 기본 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="63f55-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="63f55-148">ApplicationDbContext</span></span>

<span data-ttu-id="63f55-149">`ApplicationDbContext`(*Data/ApplicationDbContext*)에서 <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> IdentityServer에 대 한 스키마를 포함 하도록를 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="63f55-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="63f55-151">데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 클래스 중 하나에서 상속 하 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 고 Identity 메서드를 호출 하 여 스키마를 포함 하도록 컨텍스트를 구성 합니다 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="63f55-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="63f55-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="63f55-152">OidcConfigurationController</span></span>

<span data-ttu-id="63f55-153">`OidcConfigurationController`(*Controller/OidcConfigurationController*)에서 클라이언트 끝점은 oidc 매개 변수를 제공 하도록 프로 비전 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="63f55-154">앱 설정 파일</span><span class="sxs-lookup"><span data-stu-id="63f55-154">App settings files</span></span>

<span data-ttu-id="63f55-155">프로젝트 루트의 앱 설정 파일 (*appsettings.js*)에서 `IdentityServer` 섹션은 구성 된 클라이언트 목록에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="63f55-156">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-156">In the following example, there's a single client.</span></span> <span data-ttu-id="63f55-157">클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="63f55-158">프로필은 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="63f55-159">프로필은 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="63f55-160">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름입니다 (예: `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="63f55-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="63f55-161">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="63f55-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="63f55-162">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="63f55-162">Authentication package</span></span>

<span data-ttu-id="63f55-163">개별 사용자 계정 ()을 사용 하도록 앱을 만들 때 앱 `Individual` 은 앱의 프로젝트 파일에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="63f55-164">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="63f55-165">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="63f55-166">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="63f55-166">API authorization support</span></span>

<span data-ttu-id="63f55-167">사용자 인증에 대 한 지원은 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 내에서 제공 하는 확장 메서드에 의해 서비스 컨테이너에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="63f55-168">이 메서드는 앱에서 기존 권한 부여 시스템과 상호 작용 하는 데 필요한 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="63f55-169">기본적으로 앱에 대 한 구성은에서 규칙에 따라 로드 됩니다 `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="63f55-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="63f55-170">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="63f55-171">옵션으로 오버 로드를 호출 하 여 별도의 끝점을 가리키도록이 URL을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="63f55-172">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="63f55-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="63f55-173">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="63f55-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="63f55-174">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="63f55-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="63f55-175">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="63f55-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="63f55-176">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="63f55-176">LoginDisplay component</span></span>

<span data-ttu-id="63f55-177">구성 `LoginDisplay` 요소 (*Shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*shared/mainlayout. razor*)에서 렌더링 되 고 다음 동작을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="63f55-178">인증 된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="63f55-178">For authenticated users:</span></span>
  * <span data-ttu-id="63f55-179">현재 사용자 이름을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-179">Displays the current user name.</span></span>
  * <span data-ttu-id="63f55-180">ASP.NET Core의 사용자 프로필 페이지에 대 한 링크를 제공 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="63f55-181">앱에서 로그 아웃할 수 있는 단추를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="63f55-182">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="63f55-182">For anonymous users:</span></span>
  * <span data-ttu-id="63f55-183">등록할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-183">Offers the option to register.</span></span>
  * <span data-ttu-id="63f55-184">로그인 할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="63f55-185">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="63f55-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="63f55-186">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="63f55-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="63f55-187">앱 실행</span><span class="sxs-lookup"><span data-stu-id="63f55-187">Run the app</span></span>

<span data-ttu-id="63f55-188">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-188">Run the app from the Server project.</span></span> <span data-ttu-id="63f55-189">Visual Studio를 사용 하는 경우 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="63f55-190">도구 모음에서 **시작 프로젝트** 드롭다운 목록을 *서버 API 앱* 으로 설정 하 고 **실행** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="63f55-191">**솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="63f55-192">API 권한 부여를 사용 하 여 이름 및 역할 클레임</span><span class="sxs-lookup"><span data-stu-id="63f55-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="63f55-193">사용자 지정 사용자 팩터리</span><span class="sxs-lookup"><span data-stu-id="63f55-193">Custom user factory</span></span>

<span data-ttu-id="63f55-194">클라이언트 앱에서 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="63f55-195">서버는 단일 클레임에서 여러 역할을 JSON 배열로 보냅니다 `role` .</span><span class="sxs-lookup"><span data-stu-id="63f55-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="63f55-196">단일 역할은 클레임에서 문자열 값으로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="63f55-197">팩터리는 `role` 각 사용자의 역할에 대 한 개별 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="63f55-198">*CustomUserFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="63f55-198">*CustomUserFactory.cs*:</span></span>

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

<span data-ttu-id="63f55-199">클라이언트 앱에서 팩터리 `Program.Main` (*Program.cs*)를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="63f55-200">서버 앱에서 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 작성기에 대해를 호출 하면 Identity 역할 관련 서비스가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="63f55-201">Identity서버 구성</span><span class="sxs-lookup"><span data-stu-id="63f55-201">Configure Identity Server</span></span>

<span data-ttu-id="63f55-202">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="63f55-203">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="63f55-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="63f55-204">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="63f55-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="63f55-205">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="63f55-205">API authorization options</span></span>

<span data-ttu-id="63f55-206">서버 앱에서:</span><span class="sxs-lookup"><span data-stu-id="63f55-206">In the Server app:</span></span>

* <span data-ttu-id="63f55-207">Identity `name` 및 `role` 클레임을 ID 토큰 및 액세스 토큰에 추가 하도록 서버를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="63f55-208">JWT 토큰 처리기에서 역할에 대 한 기본 매핑을 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="63f55-209">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="63f55-209">Profile Service</span></span>

<span data-ttu-id="63f55-210">서버 앱에서 `ProfileService` 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="63f55-211">*ProfileService.cs*:</span><span class="sxs-lookup"><span data-stu-id="63f55-211">*ProfileService.cs*:</span></span>

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

<span data-ttu-id="63f55-212">서버 앱에서 프로필 서비스를 등록 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="63f55-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="63f55-213">권한 부여 메커니즘 사용</span><span class="sxs-lookup"><span data-stu-id="63f55-213">Use authorization mechanisms</span></span>

<span data-ttu-id="63f55-214">클라이언트 앱에서는이 시점에서 구성 요소 권한 부여 방법이 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="63f55-215">구성 요소의 권한 부여 메커니즘은 역할을 사용 하 여 사용자에 게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="63f55-216">[AuthorizeView 구성 요소](xref:security/blazor/index#authorizeview-component) (예: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="63f55-216">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="63f55-217">[ `[Authorize]` attribute 지시문](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (예: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="63f55-217">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="63f55-218">[절차적 논리](xref:security/blazor/index#procedural-logic) (예: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="63f55-218">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="63f55-219">여러 역할 테스트가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="63f55-220">`User.Identity.Name`는 클라이언트 앱에서 사용자의 사용자 이름 (일반적으로 로그인 메일 주소)으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="63f55-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="63f55-221">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="63f55-221">Additional resources</span></span>

* [<span data-ttu-id="63f55-222">Azure App Service 배포</span><span class="sxs-lookup"><span data-stu-id="63f55-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="63f55-223">Key Vault에서 인증서 가져오기 (Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="63f55-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="63f55-224">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="63f55-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
