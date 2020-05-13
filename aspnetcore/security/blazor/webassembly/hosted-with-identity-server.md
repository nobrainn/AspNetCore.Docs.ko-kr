---
title: Blazor서버를 사용 하 여 ASP.NET Core weasemboman 호스팅된 앱 보안 Identity
author: guardrex
description: Blazor [IdentityServer](https://identityserver.io/) 백 엔드를 사용 하는 Visual Studio 내에서 인증을 사용 하 여 새 호스트 된 앱을 만들려면
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 2ab43ac5f4de398c57707de23a06a1650f6140cb
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153628"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="95e63-103">Blazor서버를 사용 하 여 ASP.NET Core weasemboman 호스팅된 앱 보안 Identity</span><span class="sxs-lookup"><span data-stu-id="95e63-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="95e63-104">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="95e63-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="95e63-105">Blazor [IdentityServer](https://identityserver.io/) 를 사용 하 여 사용자 및 API 호출을 인증 하는 Visual Studio에서 새로 호스팅된 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="95e63-106">Visual Studio를 사용 하 여 새 \*\* Blazor weasembomapp\*\* 을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="95e63-107">자세한 내용은 <xref:blazor/get-started>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="95e63-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="95e63-108">**새 Blazor 앱 만들기** 대화 상자의 **인증** 섹션에서 **변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="95e63-109">**개별 사용자 계정** , **확인을**차례로 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="95e63-110">**고급** 섹션에서 **호스팅된 ASP.NET Core** 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="95e63-111">**만들기** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-111">Select the **Create** button.</span></span>

<span data-ttu-id="95e63-112">명령 셸에서 앱을 만들려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="95e63-113">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="95e63-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="95e63-114">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="95e63-115">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="95e63-115">Server app configuration</span></span>

<span data-ttu-id="95e63-116">다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="95e63-117">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="95e63-117">Startup class</span></span>

<span data-ttu-id="95e63-118">클래스에는 `Startup` 다음과 같은 추가 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="95e63-119">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="95e63-119">In `Startup.ConfigureServices`:</span></span>

  * Identity<span data-ttu-id="95e63-120">:</span><span class="sxs-lookup"><span data-stu-id="95e63-120">:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="95e63-121">IdentityServer <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> IdentityServer 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 도우미 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="95e63-122"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>IdentityServer에서 생성 한 JWT 토큰의 유효성을 검사 하도록 앱을 구성 하는 추가 도우미 메서드를 사용 하 여 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="95e63-123">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="95e63-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="95e63-124">요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="95e63-125">OIDC (Open ID Connect) 끝점을 노출 하는 IdentityServer 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="95e63-126">인증 및 권한 부여 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="95e63-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="95e63-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="95e63-127">AddApiAuthorization</span></span>

<span data-ttu-id="95e63-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>도우미 메서드는 ASP.NET Core 시나리오에 대 한 [IdentityServer](https://identityserver.io/) 를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="95e63-129">IdentityServer는 앱 보안 문제를 처리 하기 위한 강력 하 고 확장 가능한 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="95e63-130">IdentityServer는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="95e63-131">따라서 유용한 시작 지점을 고려 하는 규칙 집합과 구성 옵션이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="95e63-132">인증을 변경 해야 하는 경우 앱의 요구 사항에 맞게 인증을 사용자 지정 하기 위해 IdentityServer의 모든 기능을 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="95e63-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="95e63-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="95e63-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="95e63-135">정책은 Identity URL 공간의 모든 하위 경로로 라우팅되는 모든 요청을 처리할 수 있도록 구성 됩니다 Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="95e63-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="95e63-136">는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 다른 모든 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="95e63-137">또한이 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-137">Additionally, this method:</span></span>

* <span data-ttu-id="95e63-138">`{APPLICATION NAME}API`기본 범위인 IdentityServer를 사용 하 여 API 리소스를 등록 `{APPLICATION NAME}API` 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="95e63-139">앱에 대해 IdentityServer에서 발급 한 토큰의 유효성을 검사 하도록 JWT 전달자 토큰 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="95e63-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="95e63-140">WeatherForecastController</span></span>

<span data-ttu-id="95e63-141">`WeatherForecastController`(*Controller/WeatherForecastController*)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 클래스에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="95e63-142">특성은 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한이 부여 되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="95e63-143">기본 권한 부여 정책은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 앞에서 언급 한 정책 체계에 의해 설정 되는 기본 인증 체계를 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="95e63-144">도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 를 앱에 대 한 요청에 대 한 기본 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="95e63-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="95e63-145">ApplicationDbContext</span></span>

<span data-ttu-id="95e63-146">`ApplicationDbContext`(*Data/ApplicationDbContext*)에서 <xref:Microsoft.EntityFrameworkCore.DbContext> Identity <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> IdentityServer에 대 한 스키마를 포함 하도록 확장 하는 예외와 함께에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="95e63-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="95e63-148">데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 클래스 중 하나에서 상속 하 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 고 Identity 메서드를 호출 하 여 스키마를 포함 하도록 컨텍스트를 구성 합니다 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="95e63-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="95e63-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="95e63-149">OidcConfigurationController</span></span>

<span data-ttu-id="95e63-150">`OidcConfigurationController`(*Controller/OidcConfigurationController*)에서 클라이언트 끝점은 oidc 매개 변수를 제공 하도록 프로 비전 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="95e63-151">앱 설정 파일</span><span class="sxs-lookup"><span data-stu-id="95e63-151">App settings files</span></span>

<span data-ttu-id="95e63-152">프로젝트 루트의 앱 설정 파일 (*appsettings*)에서 `IdentityServer` 섹션은 구성 된 클라이언트 목록에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="95e63-153">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-153">In the following example, there's a single client.</span></span> <span data-ttu-id="95e63-154">클라이언트 이름은 앱 이름에 해당 하며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="95e63-155">프로필은 구성 중인 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="95e63-156">프로필은 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="95e63-157">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="95e63-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="95e63-158">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="95e63-158">Authentication package</span></span>

<span data-ttu-id="95e63-159">개별 사용자 계정 ()을 사용 하도록 앱을 만들면 앱 `Individual` 은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 앱의 프로젝트 파일에서 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="95e63-160">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="95e63-161">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="95e63-162">`{VERSION}`위의 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` 문서에 표시 된 패키지의 버전으로 바꿉니다 <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="95e63-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="95e63-163">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="95e63-163">API authorization support</span></span>

<span data-ttu-id="95e63-164">사용자 인증에 대 한 지원은 패키지 내에서 제공 된 확장 메서드에 의해 서비스 컨테이너에 연결 됩니다 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="95e63-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="95e63-165">이 메서드는 앱이 기존 인증 시스템과 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="95e63-166">기본적으로에서 규칙에 따라 앱에 대 한 구성을 로드 합니다 `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="95e63-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="95e63-167">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="95e63-168">옵션으로 오버 로드를 호출 하 여 별도의 끝점을 가리키도록이 URL을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="95e63-169">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="95e63-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="95e63-170">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="95e63-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="95e63-171">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95e63-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="95e63-172">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95e63-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="95e63-173">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95e63-173">LoginDisplay component</span></span>

<span data-ttu-id="95e63-174">구성 `LoginDisplay` 요소 (*Shared/LoginDisplay*)는 `MainLayout` 구성 요소 (*shared/mainlayout. razor*)에서 렌더링 되 고 다음 동작을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="95e63-175">인증 된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="95e63-175">For authenticated users:</span></span>
  * <span data-ttu-id="95e63-176">현재 사용자 이름을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-176">Displays the current user name.</span></span>
  * <span data-ttu-id="95e63-177">ASP.NET Core의 사용자 프로필 페이지에 대 한 링크를 제공 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="95e63-178">앱에서 로그 아웃할 수 있는 단추를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="95e63-179">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="95e63-179">For anonymous users:</span></span>
  * <span data-ttu-id="95e63-180">등록할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-180">Offers the option to register.</span></span>
  * <span data-ttu-id="95e63-181">로그인 할 수 있는 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="95e63-182">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95e63-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="95e63-183">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="95e63-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="95e63-184">앱 실행</span><span class="sxs-lookup"><span data-stu-id="95e63-184">Run the app</span></span>

<span data-ttu-id="95e63-185">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-185">Run the app from the Server project.</span></span> <span data-ttu-id="95e63-186">Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="95e63-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="95e63-187">추가 자료</span><span class="sxs-lookup"><span data-stu-id="95e63-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="95e63-188">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="95e63-188">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
