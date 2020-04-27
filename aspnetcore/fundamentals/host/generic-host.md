---
title: .NET 일반 호스트
author: rick-anderson
description: 앱 시작 및 수명 관리를 담당하는 .NET Core 일반 호스트에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488776"
---
# <a name="net-generic-host"></a><span data-ttu-id="fc869-103">.NET 일반 호스트</span><span class="sxs-lookup"><span data-stu-id="fc869-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="fc869-104">ASP.NET Core 템플릿은 .NET Core 일반 호스트 <xref:Microsoft.Extensions.Hosting.HostBuilder>를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="fc869-105">호스트 정의</span><span class="sxs-lookup"><span data-stu-id="fc869-105">Host definition</span></span>

<span data-ttu-id="fc869-106">*호스트*는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="fc869-107">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="fc869-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="fc869-108">로깅</span><span class="sxs-lookup"><span data-stu-id="fc869-108">Logging</span></span>
* <span data-ttu-id="fc869-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="fc869-109">Configuration</span></span>
* <span data-ttu-id="fc869-110">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="fc869-110">`IHostedService` implementations</span></span>

<span data-ttu-id="fc869-111">호스트가 시작되면 DI 컨테이너에서 찾은 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현 시 `IHostedService.StartAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="fc869-112">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="fc869-113">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fc869-114">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-114">Set up a host</span></span>

<span data-ttu-id="fc869-115">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="fc869-116">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-116">The `Main` method:</span></span>

* <span data-ttu-id="fc869-117">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="fc869-118">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="fc869-119">ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 일반 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="fc869-120">다음 코드는 비 HTTP 워크로드를 사용하여 일반 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="fc869-121">`IHostedService` 구현은 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="fc869-122">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="fc869-123">위의 코드는 ASP.NET Core 템플릿에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="fc869-124">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="fc869-125">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="fc869-126">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="fc869-127">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-127">Default builder settings</span></span>

<span data-ttu-id="fc869-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="fc869-129">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="fc869-130">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="fc869-131">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="fc869-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="fc869-132">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="fc869-132">Command-line arguments.</span></span>
* <span data-ttu-id="fc869-133">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="fc869-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fc869-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="fc869-135">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="fc869-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="fc869-136">[비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우</span><span class="sxs-lookup"><span data-stu-id="fc869-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="fc869-137">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="fc869-137">Environment variables.</span></span>
  * <span data-ttu-id="fc869-138">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="fc869-138">Command-line arguments.</span></span>
* <span data-ttu-id="fc869-139">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="fc869-140">Console</span><span class="sxs-lookup"><span data-stu-id="fc869-140">Console</span></span>
  * <span data-ttu-id="fc869-141">Debug</span><span class="sxs-lookup"><span data-stu-id="fc869-141">Debug</span></span>
  * <span data-ttu-id="fc869-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="fc869-142">EventSource</span></span>
  * <span data-ttu-id="fc869-143">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="fc869-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="fc869-144">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="fc869-145">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="fc869-146">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="fc869-147">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="fc869-148">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="fc869-149">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="fc869-150">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="fc869-151">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-151">Enables IIS integration.</span></span> <span data-ttu-id="fc869-152">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="fc869-153">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fc869-154">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="fc869-154">Framework-provided services</span></span>

<span data-ttu-id="fc869-155">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="fc869-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="fc869-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="fc869-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fc869-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="fc869-159">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="fc869-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="fc869-161"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="fc869-162">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="fc869-163">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="fc869-164">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="fc869-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-165">IHostLifetime</span></span>

<span data-ttu-id="fc869-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="fc869-167">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-167">The last implementation registered is used.</span></span>

<span data-ttu-id="fc869-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="fc869-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="fc869-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="fc869-170"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="fc869-171">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="fc869-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fc869-172">IHostEnvironment</span></span>

<span data-ttu-id="fc869-173"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="fc869-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fc869-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="fc869-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fc869-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="fc869-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="fc869-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="fc869-177">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fc869-178">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-178">Host configuration</span></span>

<span data-ttu-id="fc869-179">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="fc869-180">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="fc869-181">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="fc869-182">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fc869-183">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-184">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fc869-185">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="fc869-186">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="fc869-187">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fc869-188">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fc869-189">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="fc869-190">앱 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-190">App configuration</span></span>

<span data-ttu-id="fc869-191">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fc869-192">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-193">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="fc869-194">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="fc869-195">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="fc869-196">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="fc869-197">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-197">Settings for all app types</span></span>

<span data-ttu-id="fc869-198">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="fc869-199">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="fc869-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fc869-200">ApplicationName</span></span>

<span data-ttu-id="fc869-201">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="fc869-202">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="fc869-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fc869-203">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-203">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-204">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="fc869-205">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="fc869-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="fc869-206">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="fc869-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-207">ContentRoot</span></span>

<span data-ttu-id="fc869-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="fc869-209">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="fc869-210">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fc869-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fc869-211">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-211">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-212">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="fc869-213">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="fc869-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="fc869-214">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="fc869-215">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-215">For more information, see:</span></span>

* [<span data-ttu-id="fc869-216">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="fc869-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="fc869-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="fc869-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fc869-218">EnvironmentName</span></span>

<span data-ttu-id="fc869-219">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="fc869-220">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fc869-221">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="fc869-222">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="fc869-222">**Key**: `environment`</span></span>  
<span data-ttu-id="fc869-223">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-223">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-224">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="fc869-224">**Default**: `Production`</span></span>  
<span data-ttu-id="fc869-225">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="fc869-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="fc869-226">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="fc869-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="fc869-227">ShutdownTimeout</span></span>

<span data-ttu-id="fc869-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fc869-229">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-229">The default value is five seconds.</span></span>  <span data-ttu-id="fc869-230">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="fc869-231">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="fc869-232">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="fc869-233">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="fc869-234">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="fc869-235">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="fc869-236">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="fc869-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="fc869-237">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="fc869-237">**Type**: `int`</span></span>  
<span data-ttu-id="fc869-238">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="fc869-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="fc869-239">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="fc869-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="fc869-240">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="fc869-241">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="fc869-242">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-242">Settings for web apps</span></span>

<span data-ttu-id="fc869-243">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="fc869-244">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="fc869-245">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="fc869-246">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="fc869-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="fc869-247">CaptureStartupErrors</span></span>

<span data-ttu-id="fc869-248">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="fc869-249">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="fc869-250">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="fc869-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="fc869-251">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-252">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="fc869-253">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="fc869-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="fc869-254">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="fc869-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="fc869-255">DetailedErrors</span></span>

<span data-ttu-id="fc869-256">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="fc869-257">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="fc869-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="fc869-258">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-259">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="fc869-259">**Default**: `false`</span></span>  
<span data-ttu-id="fc869-260">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="fc869-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="fc869-261">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="fc869-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="fc869-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="fc869-263">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="fc869-264">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="fc869-265">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="fc869-266">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fc869-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="fc869-267">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-267">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-268">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="fc869-268">**Default**: Empty string</span></span>  
<span data-ttu-id="fc869-269">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fc869-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="fc869-270">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="fc869-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="fc869-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="fc869-272">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="fc869-273">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fc869-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="fc869-274">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-274">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-275">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="fc869-275">**Default**: Empty string</span></span>  
<span data-ttu-id="fc869-276">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fc869-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="fc869-277">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="fc869-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="fc869-278">HTTPS_Port</span></span>

<span data-ttu-id="fc869-279">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="fc869-279">The HTTPS redirect port.</span></span> <span data-ttu-id="fc869-280">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fc869-281">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="fc869-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="fc869-282">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-282">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-283">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="fc869-284">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="fc869-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="fc869-285">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="fc869-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="fc869-286">PreferHostingUrls</span></span>

<span data-ttu-id="fc869-287">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="fc869-288">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="fc869-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="fc869-289">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-290">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="fc869-290">**Default**: `true`</span></span>  
<span data-ttu-id="fc869-291">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="fc869-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="fc869-292">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="fc869-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="fc869-293">PreventHostingStartup</span></span>

<span data-ttu-id="fc869-294">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="fc869-295">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="fc869-296">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="fc869-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="fc869-297">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-298">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="fc869-298">**Default**: `false`</span></span>  
<span data-ttu-id="fc869-299">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="fc869-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="fc869-300">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="fc869-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="fc869-301">StartupAssembly</span></span>

<span data-ttu-id="fc869-302">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="fc869-303">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="fc869-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="fc869-304">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-304">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-305">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fc869-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="fc869-306">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="fc869-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="fc869-307">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="fc869-308">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="fc869-309">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="fc869-310">URL</span><span class="sxs-lookup"><span data-stu-id="fc869-310">URLs</span></span>

<span data-ttu-id="fc869-311">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="fc869-312">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="fc869-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="fc869-313">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="fc869-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="fc869-314">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="fc869-315">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="fc869-316">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="fc869-316">**Key**: `urls`</span></span>  
<span data-ttu-id="fc869-317">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-317">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-318">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="fc869-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="fc869-319">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="fc869-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="fc869-320">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="fc869-321">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="fc869-322">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="fc869-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-323">WebRoot</span></span>

<span data-ttu-id="fc869-324">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="fc869-325">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="fc869-326">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="fc869-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="fc869-327">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-327">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-328">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="fc869-329">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="fc869-330">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="fc869-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="fc869-331">이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="fc869-332">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-332">For more information, see:</span></span>

* [<span data-ttu-id="fc869-333">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="fc869-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="fc869-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="fc869-335">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="fc869-335">Manage the host lifetime</span></span>

<span data-ttu-id="fc869-336">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="fc869-337">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fc869-338">Run</span><span class="sxs-lookup"><span data-stu-id="fc869-338">Run</span></span>

<span data-ttu-id="fc869-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="fc869-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-340">RunAsync</span></span>

<span data-ttu-id="fc869-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="fc869-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-342">RunConsoleAsync</span></span>

<span data-ttu-id="fc869-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="fc869-344">Start</span><span class="sxs-lookup"><span data-stu-id="fc869-344">Start</span></span>

<span data-ttu-id="fc869-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="fc869-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-346">StartAsync</span></span>

<span data-ttu-id="fc869-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="fc869-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fc869-349">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="fc869-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-350">StopAsync</span></span>

<span data-ttu-id="fc869-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="fc869-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fc869-352">WaitForShutdown</span></span>

<span data-ttu-id="fc869-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="fc869-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="fc869-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="fc869-356">외부 제어</span><span class="sxs-lookup"><span data-stu-id="fc869-356">External control</span></span>

<span data-ttu-id="fc869-357">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc869-358">ASP.NET Core 앱은 호스트를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="fc869-359">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="fc869-360">이 문서에서는 HTTP 요청을 처리하지 않는 앱에 사용되는 ASP.NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="fc869-361">일반 호스트의 목적은 웹 호스트 API에서 HTTP 파이프라인을 분리하여 호스트 시나리오의 더 광범위한 배열을 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="fc869-362">일반 호스트에 기반을 둔 메시징, 백그라운드 작업 및 기타 HTTP 이외 워크로드는 구성, DI(종속성 주입) 및 로깅과 같은 교차 편집 기능에서 이점을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="fc869-363">일반 호스트는 ASP.NET Core 2.1의 새로운 기능이며 웹 호스팅 시나리오에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="fc869-364">웹 호스팅 시나리오의 경우 [웹 호스트](xref:fundamentals/host/web-host)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="fc869-365">일반 호스트는 향후 릴리스에서 웹 호스트를 대체하고 HTTP 및 HTTP 이외 시나리오에서 기본 호스트 API 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="fc869-366">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fc869-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fc869-367">[Visual Studio Code](https://code.visualstudio.com/)에서 샘플 앱을 실행할 때는 *외부 또는 통합 터미널*을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="fc869-368">`internalConsole`에서는 샘플을 실행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="fc869-369">Visual Studio Code에서 콘솔을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="fc869-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="fc869-370">*.vscode/launch.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="fc869-371">**.NET Core 시작(콘솔)** 구성에서 **콘솔** 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="fc869-372">값을 `externalTerminal` 또는 `integratedTerminal` 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="fc869-373">소개</span><span class="sxs-lookup"><span data-stu-id="fc869-373">Introduction</span></span>

<span data-ttu-id="fc869-374">일반 호스트 라이브러리는 <xref:Microsoft.Extensions.Hosting> 네임스페이스에서 사용할 수 있으며, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="fc869-375">`Microsoft.Extensions.Hosting` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="fc869-376"><xref:Microsoft.Extensions.Hosting.IHostedService>는 코드 실행 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="fc869-377">각 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현은 [ConfigureServices의 서비스 등록](#configureservices) 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="fc869-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>는 호스트가 시작될 때 각 <xref:Microsoft.Extensions.Hosting.IHostedService>에서 호출되고, <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>는 호스트가 점진적으로 종료될 때 등록 순서의 역순으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fc869-379">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-379">Set up a host</span></span>

<span data-ttu-id="fc869-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>는 라이브러리 및 앱이 호스트를 초기화, 빌드 및 실행하는 데 사용하는 주 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="fc869-381">옵션</span><span class="sxs-lookup"><span data-stu-id="fc869-381">Options</span></span>

<span data-ttu-id="fc869-382"><xref:Microsoft.Extensions.Hosting.HostOptions>는 <xref:Microsoft.Extensions.Hosting.IHost>에 대한 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="fc869-383">시스템 종료 시간 제한</span><span class="sxs-lookup"><span data-stu-id="fc869-383">Shutdown timeout</span></span>

<span data-ttu-id="fc869-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fc869-385">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-385">The default value is five seconds.</span></span>

<span data-ttu-id="fc869-386">`Program.Main`의 다음 옵션 구성은 기본 5초 시스템 종료 시간 제한을 20초로 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="fc869-387">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="fc869-387">Default services</span></span>

<span data-ttu-id="fc869-388">호스트 초기화 중에 다음 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="fc869-389">[환경](xref:fundamentals/environments)(<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="fc869-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="fc869-390">[구성](xref:fundamentals/configuration/index)(<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="fc869-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="fc869-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="fc869-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="fc869-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="fc869-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="fc869-393">[옵션](xref:fundamentals/configuration/options)(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="fc869-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="fc869-394">[로깅](xref:fundamentals/logging/index)(<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="fc869-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fc869-395">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-395">Host configuration</span></span>

<span data-ttu-id="fc869-396">호스트 구성은 다음에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-396">Host configuration is created by:</span></span>

* <span data-ttu-id="fc869-397"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 호출하여 [콘텐츠 루트](#content-root) 및 [환경](#environment)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="fc869-398"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>의 구성 공급자에서 구성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="fc869-399">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="fc869-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="fc869-400">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="fc869-400">Application key (name)</span></span>

<span data-ttu-id="fc869-401">호스트를 생성하는 동안 호스트 구성에서 [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="fc869-402">값을 명시적으로 설정하려면 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="fc869-403">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="fc869-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fc869-404">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-404">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-405">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="fc869-406">**설정 방법**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="fc869-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="fc869-407">**환경 변수**: `<PREFIX_>APPLICATIONNAME`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fc869-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="fc869-408">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="fc869-408">Content root</span></span>

<span data-ttu-id="fc869-409">이 설정은 호스트가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="fc869-410">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fc869-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fc869-411">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-411">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-412">**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="fc869-413">**설정 방법**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="fc869-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="fc869-414">**환경 변수**: `<PREFIX_>CONTENTROOT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fc869-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="fc869-415">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="fc869-416">자세한 내용은 [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="fc869-417">환경</span><span class="sxs-lookup"><span data-stu-id="fc869-417">Environment</span></span>

<span data-ttu-id="fc869-418">앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="fc869-419">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="fc869-419">**Key**: `environment`</span></span>  
<span data-ttu-id="fc869-420">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-420">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-421">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="fc869-421">**Default**: `Production`</span></span>  
<span data-ttu-id="fc869-422">**설정 방법**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="fc869-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="fc869-423">**환경 변수**: `<PREFIX_>ENVIRONMENT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="fc869-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="fc869-424">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-424">The environment can be set to any value.</span></span> <span data-ttu-id="fc869-425">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fc869-426">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="fc869-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="fc869-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="fc869-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 호스트에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="fc869-429">호스트 구성은 앱의 빌드 프로세스에 사용할 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="fc869-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-431">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fc869-432">기본적으로 공급자는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-432">No providers are included by default.</span></span> <span data-ttu-id="fc869-433">다음을 포함하여 앱에 필요한 모든 구성 공급자를 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>에 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="fc869-434">구성 파일(예: *hostsettings.json* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="fc869-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="fc869-435">환경 변수 구성.</span><span class="sxs-lookup"><span data-stu-id="fc869-435">Environment variable configuration.</span></span>
* <span data-ttu-id="fc869-436">명령줄 인수 구성.</span><span class="sxs-lookup"><span data-stu-id="fc869-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="fc869-437">기타 필수 구성 공급자.</span><span class="sxs-lookup"><span data-stu-id="fc869-437">Any other required configuration providers.</span></span>

<span data-ttu-id="fc869-438">호스트의 파일 구성은 `SetBasePath`를 사용하여 앱의 기본 경로를 지정한 후 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 중 하나를 호출하여 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="fc869-439">샘플 앱은 JSON 파일인 *hostsettings.json*을 사용하고 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>을 호출하여 파일의 호스트 구성 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="fc869-440">[환경 변수 구성](xref:fundamentals/configuration/index#environment-variables-configuration-provider)을 추가하려면 호스트 작성기에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="fc869-441">`AddEnvironmentVariables`는 선택적 사용자 정의 접두사를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="fc869-442">샘플 앱은 `PREFIX_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="fc869-443">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fc869-444">샘플 앱의 호스트가 구성되면 `PREFIX_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fc869-445">[Visual Studio](https://visualstudio.microsoft.com)를 사용하거나 `dotnet run`을 통해 앱을 실행할 때 개발하는 동안에 환경 변수는 *Properties/launchSettings.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="fc869-446">[Visual Studio Code](https://code.visualstudio.com/)에서 환경 변수는 개발하는 동안에 *.vscode/launch.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="fc869-447">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="fc869-448">[명령줄 구성](xref:fundamentals/configuration/index#command-line-configuration-provider)은 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>을 호출하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="fc869-449">명령줄 구성이 마지막으로 추가되어 명령줄 인수가 이전 구성 공급자가 제공한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="fc869-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fc869-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="fc869-451">[applicationName](#application-key-name) 및 [contentRoot](#content-root) 키를 사용하여 추가 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="fc869-452"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 사용한 `HostBuilder` 구성 예:</span><span class="sxs-lookup"><span data-stu-id="fc869-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="fc869-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="fc869-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="fc869-454">앱 구성은 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="fc869-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 앱에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="fc869-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-457">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="fc869-458"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 의해 생성된 구성은 다음 작업에 대한 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 및 <xref:Microsoft.Extensions.Hosting.IHost.Services*>에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="fc869-459">앱 구성은 [ConfigureHostConfiguration](#configurehostconfiguration)에서 제공하는 호스트 구성을 자동으로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="fc869-460"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 사용한 앱 구성 예:</span><span class="sxs-lookup"><span data-stu-id="fc869-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="fc869-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fc869-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="fc869-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="fc869-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="fc869-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="fc869-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="fc869-464">출력 디렉터리로 설정 파일을 이동하려면, 설정 파일을 프로젝트 파일 내 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="fc869-465">샘플 앱은 다음 `<Content>` 항목과 함께 JSON 앱 설정 파일과 *hostsettings.json*을 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="fc869-466"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 및 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>와 같은 구성 확장 메서드에는 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 및 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)와 같은 추가 NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="fc869-467">앱에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 경우가 아니면 이 패키지는 코어 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 패키지에 추가로 프로젝트에 추가되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="fc869-468">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="fc869-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="fc869-469">ConfigureServices</span></span>

<span data-ttu-id="fc869-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>는 앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="fc869-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="fc869-472">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="fc869-473">자세한 내용은 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="fc869-474">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `AddHostedService` 확장 메서드를 사용하여 수명 이벤트, `LifetimeEventsHostedService` 및 시간 제한 백그라운드 작업에 대한 서비스 `TimedHostedService`를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="fc869-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="fc869-475">ConfigureLogging</span></span>

<span data-ttu-id="fc869-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>에는 제공된 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>를 구성하는 대리자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="fc869-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="fc869-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-478">UseConsoleLifetime</span></span>

<span data-ttu-id="fc869-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="fc869-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="fc869-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 수명 구현으로 미리 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="fc869-482">등록된 마지막 수명이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="fc869-483">컨테이너 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-483">Container configuration</span></span>

<span data-ttu-id="fc869-484">호스트는 다른 컨테이너 연결을 지원하기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="fc869-485">팩터리 제공은 DI 컨테이너 등록의 일부가 아니지만 구체적 DI 컨테이너를 만드는 데 사용되는 내장 호스트입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="fc869-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*)는 앱의 서비스 공급자를 만드는 데 사용되는 기본 팩터리를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="fc869-487">사용자 지정 컨테이너 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 메서드로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="fc869-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>는 기본 호스트 API 위에 컨테이너를 구성하기 위한 강력한 형식의 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="fc869-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="fc869-490">앱에 대한 서비스 컨테이너를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="fc869-491">서비스 컨테이너 팩터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="fc869-492">팩터리를 사용하고 앱에 대 한 사용자 지정 서비스 컨테이너를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="fc869-493">확장성</span><span class="sxs-lookup"><span data-stu-id="fc869-493">Extensibility</span></span>

<span data-ttu-id="fc869-494"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 사용하여 호스트 확장성이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="fc869-495">다음 예제는 <xref:fundamentals/host/hosted-services>에 설명된 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 예제를 사용하여 확장 메서드가 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현을 확장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="fc869-496">앱은 `UseHostedService` 확장 메서드를 설정하여 `T`에서 전달되는 호스티드 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="fc869-497">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="fc869-497">Manage the host</span></span>

<span data-ttu-id="fc869-498"><xref:Microsoft.Extensions.Hosting.IHost> 구현은 서비스 컨테이너에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현의 시작 및 중지를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fc869-499">Run</span><span class="sxs-lookup"><span data-stu-id="fc869-499">Run</span></span>

<span data-ttu-id="fc869-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="fc869-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-501">RunAsync</span></span>

<span data-ttu-id="fc869-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="fc869-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-503">RunConsoleAsync</span></span>

<span data-ttu-id="fc869-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="fc869-505">Start 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-505">Start and StopAsync</span></span>

<span data-ttu-id="fc869-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="fc869-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="fc869-508">StartAsync 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="fc869-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>가 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="fc869-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>가 서버를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="fc869-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fc869-511">WaitForShutdown</span></span>

<span data-ttu-id="fc869-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`(<kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM 수신 대기)과 같이 <xref:Microsoft.Extensions.Hosting.IHostLifetime>을 통해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="fc869-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="fc869-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="fc869-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="fc869-516">외부 제어</span><span class="sxs-lookup"><span data-stu-id="fc869-516">External control</span></span>

<span data-ttu-id="fc869-517">외부에서 호출할 수 있는 메서드를 사용하여 호스트의 외부 제어를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="fc869-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fc869-519">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="fc869-520">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="fc869-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="fc869-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="fc869-522">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="fc869-523">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="fc869-524">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="fc869-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="fc869-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>은 점진적인 종료 요청을 비롯한 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="fc869-526">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 <xref:System.Action> 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="fc869-527">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="fc869-527">Cancellation Token</span></span> | <span data-ttu-id="fc869-528">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="fc869-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="fc869-529">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="fc869-530">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="fc869-531">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-531">All requests should be processed.</span></span> <span data-ttu-id="fc869-532">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="fc869-533">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="fc869-534">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-534">Requests may still be processing.</span></span> <span data-ttu-id="fc869-535">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="fc869-536">클래스에 대한 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 서비스 생성자 주입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="fc869-537">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `LifetimeEventsHostedService` 클래스(<xref:Microsoft.Extensions.Hosting.IHostedService> 구현)에 대한 생성자 주입을 사용하여 이벤트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="fc869-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc869-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="fc869-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="fc869-540">다음 클래스에서는 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fc869-541">ASP.NET Core 템플릿은 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="fc869-542">호스트 정의</span><span class="sxs-lookup"><span data-stu-id="fc869-542">Host definition</span></span>

<span data-ttu-id="fc869-543">*호스트*는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="fc869-544">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="fc869-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="fc869-545">로깅</span><span class="sxs-lookup"><span data-stu-id="fc869-545">Logging</span></span>
* <span data-ttu-id="fc869-546">Configuration</span><span class="sxs-lookup"><span data-stu-id="fc869-546">Configuration</span></span>
* <span data-ttu-id="fc869-547">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="fc869-547">`IHostedService` implementations</span></span>

<span data-ttu-id="fc869-548">호스트가 시작되면 DI 컨테이너에서 찾은 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현 시 `IHostedService.StartAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="fc869-549">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="fc869-550">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="fc869-551">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-551">Set up a host</span></span>

<span data-ttu-id="fc869-552">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="fc869-553">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-553">The `Main` method:</span></span>

* <span data-ttu-id="fc869-554">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="fc869-555">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="fc869-556">ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="fc869-557">다음 코드는 DI 컨테이너에 `IHostedService` 구현이 추가된 비 HTTP 워크로드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="fc869-558">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="fc869-559">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="fc869-560">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="fc869-561">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="fc869-562">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-562">Default builder settings</span></span>

<span data-ttu-id="fc869-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="fc869-564">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="fc869-565">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="fc869-566">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="fc869-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="fc869-567">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="fc869-567">Command-line arguments.</span></span>
* <span data-ttu-id="fc869-568">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="fc869-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fc869-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="fc869-570">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="fc869-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="fc869-571">[비밀 관리자](xref:security/app-secrets): 앱이 `Development` 환경에서 실행되는 경우</span><span class="sxs-lookup"><span data-stu-id="fc869-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="fc869-572">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="fc869-572">Environment variables.</span></span>
  * <span data-ttu-id="fc869-573">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="fc869-573">Command-line arguments.</span></span>
* <span data-ttu-id="fc869-574">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="fc869-575">Console</span><span class="sxs-lookup"><span data-stu-id="fc869-575">Console</span></span>
  * <span data-ttu-id="fc869-576">Debug</span><span class="sxs-lookup"><span data-stu-id="fc869-576">Debug</span></span>
  * <span data-ttu-id="fc869-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="fc869-577">EventSource</span></span>
  * <span data-ttu-id="fc869-578">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="fc869-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="fc869-579">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="fc869-580">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="fc869-581">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="fc869-582">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="fc869-583">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="fc869-584">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="fc869-585">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="fc869-586">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-586">Enables IIS integration.</span></span> <span data-ttu-id="fc869-587">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="fc869-588">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fc869-589">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="fc869-589">Framework-provided services</span></span>

<span data-ttu-id="fc869-590">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="fc869-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="fc869-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="fc869-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fc869-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="fc869-594">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="fc869-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="fc869-596"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="fc869-597">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="fc869-598">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="fc869-599">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="fc869-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="fc869-600">IHostLifetime</span></span>

<span data-ttu-id="fc869-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="fc869-602">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-602">The last implementation registered is used.</span></span>

<span data-ttu-id="fc869-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="fc869-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="fc869-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="fc869-605"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="fc869-606">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="fc869-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="fc869-607">IHostEnvironment</span></span>

<span data-ttu-id="fc869-608"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="fc869-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fc869-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="fc869-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fc869-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="fc869-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="fc869-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="fc869-612">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="fc869-613">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-613">Host configuration</span></span>

<span data-ttu-id="fc869-614">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="fc869-615">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="fc869-616">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="fc869-617">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fc869-618">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-619">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="fc869-620">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="fc869-621">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="fc869-622">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="fc869-623">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="fc869-624">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="fc869-625">앱 구성</span><span class="sxs-lookup"><span data-stu-id="fc869-625">App configuration</span></span>

<span data-ttu-id="fc869-626">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="fc869-627">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="fc869-628">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="fc869-629">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="fc869-630">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="fc869-631">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="fc869-632">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-632">Settings for all app types</span></span>

<span data-ttu-id="fc869-633">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="fc869-634">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="fc869-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="fc869-635">ApplicationName</span></span>

<span data-ttu-id="fc869-636">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="fc869-637">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="fc869-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="fc869-638">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-638">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-639">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="fc869-640">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="fc869-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="fc869-641">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="fc869-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-642">ContentRoot</span></span>

<span data-ttu-id="fc869-643">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="fc869-644">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="fc869-645">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="fc869-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="fc869-646">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-646">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-647">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="fc869-648">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="fc869-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="fc869-649">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="fc869-650">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-650">For more information, see:</span></span>

* [<span data-ttu-id="fc869-651">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="fc869-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="fc869-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="fc869-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="fc869-653">EnvironmentName</span></span>

<span data-ttu-id="fc869-654">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="fc869-655">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="fc869-656">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="fc869-657">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="fc869-657">**Key**: `environment`</span></span>  
<span data-ttu-id="fc869-658">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-658">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-659">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="fc869-659">**Default**: `Production`</span></span>  
<span data-ttu-id="fc869-660">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="fc869-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="fc869-661">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="fc869-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="fc869-662">ShutdownTimeout</span></span>

<span data-ttu-id="fc869-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="fc869-664">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-664">The default value is five seconds.</span></span>  <span data-ttu-id="fc869-665">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="fc869-666">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="fc869-667">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="fc869-668">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="fc869-669">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="fc869-670">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="fc869-671">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="fc869-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="fc869-672">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="fc869-672">**Type**: `int`</span></span>  
<span data-ttu-id="fc869-673">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="fc869-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="fc869-674">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="fc869-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="fc869-675">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="fc869-676">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="fc869-677">변경 시 앱 구성 다시 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="fc869-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="fc869-678">[기본적으로](xref:fundamentals/configuration/index#default) 파일이 변경되면 *appsettings.json* 및 *appsettings.{Environment}.json*이 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="fc869-679">ASP.NET Core 5.0 미리 보기 3 이상에서 이 다시 로드 동작을 사용하지 않도록 설정하려면 `hostBuilder:reloadConfigOnChange` 키를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="fc869-680">**키**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fc869-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="fc869-681">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-682">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="fc869-682">**Default**: `true`</span></span>  
<span data-ttu-id="fc869-683">**명령줄 인수**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fc869-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="fc869-684">**환경 변수**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="fc869-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="fc869-685">콜론(`:`) 구분 기호는 모든 플랫폼의 환경 변수 계층적 키에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="fc869-686">자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="fc869-687">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="fc869-687">Settings for web apps</span></span>

<span data-ttu-id="fc869-688">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="fc869-689">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="fc869-690">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="fc869-691">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="fc869-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="fc869-692">CaptureStartupErrors</span></span>

<span data-ttu-id="fc869-693">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="fc869-694">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="fc869-695">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="fc869-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="fc869-696">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-697">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="fc869-698">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="fc869-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="fc869-699">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="fc869-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="fc869-700">DetailedErrors</span></span>

<span data-ttu-id="fc869-701">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="fc869-702">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="fc869-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="fc869-703">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-704">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="fc869-704">**Default**: `false`</span></span>  
<span data-ttu-id="fc869-705">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="fc869-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="fc869-706">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="fc869-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="fc869-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="fc869-708">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="fc869-709">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="fc869-710">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="fc869-711">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fc869-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="fc869-712">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-712">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-713">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="fc869-713">**Default**: Empty string</span></span>  
<span data-ttu-id="fc869-714">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fc869-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="fc869-715">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="fc869-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="fc869-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="fc869-717">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="fc869-718">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="fc869-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="fc869-719">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-719">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-720">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="fc869-720">**Default**: Empty string</span></span>  
<span data-ttu-id="fc869-721">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="fc869-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="fc869-722">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="fc869-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="fc869-723">HTTPS_Port</span></span>

<span data-ttu-id="fc869-724">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="fc869-724">The HTTPS redirect port.</span></span> <span data-ttu-id="fc869-725">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fc869-726">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="fc869-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="fc869-727">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-727">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-728">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="fc869-729">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="fc869-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="fc869-730">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="fc869-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="fc869-731">PreferHostingUrls</span></span>

<span data-ttu-id="fc869-732">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="fc869-733">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="fc869-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="fc869-734">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-735">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="fc869-735">**Default**: `true`</span></span>  
<span data-ttu-id="fc869-736">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="fc869-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="fc869-737">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="fc869-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="fc869-738">PreventHostingStartup</span></span>

<span data-ttu-id="fc869-739">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="fc869-740">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="fc869-741">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="fc869-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="fc869-742">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="fc869-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="fc869-743">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="fc869-743">**Default**: `false`</span></span>  
<span data-ttu-id="fc869-744">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="fc869-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="fc869-745">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="fc869-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="fc869-746">StartupAssembly</span></span>

<span data-ttu-id="fc869-747">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="fc869-748">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="fc869-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="fc869-749">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-749">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-750">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="fc869-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="fc869-751">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="fc869-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="fc869-752">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="fc869-753">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="fc869-754">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="fc869-755">URL</span><span class="sxs-lookup"><span data-stu-id="fc869-755">URLs</span></span>

<span data-ttu-id="fc869-756">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="fc869-757">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="fc869-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="fc869-758">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="fc869-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="fc869-759">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="fc869-760">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="fc869-761">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="fc869-761">**Key**: `urls`</span></span>  
<span data-ttu-id="fc869-762">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-762">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-763">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="fc869-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="fc869-764">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="fc869-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="fc869-765">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="fc869-766">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="fc869-767">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="fc869-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-768">WebRoot</span></span>

<span data-ttu-id="fc869-769">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="fc869-770">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="fc869-771">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="fc869-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="fc869-772">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="fc869-772">**Type**: `string`</span></span>  
<span data-ttu-id="fc869-773">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="fc869-774">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="fc869-775">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="fc869-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="fc869-776">이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="fc869-777">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fc869-777">For more information, see:</span></span>

* [<span data-ttu-id="fc869-778">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="fc869-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="fc869-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="fc869-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="fc869-780">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="fc869-780">Manage the host lifetime</span></span>

<span data-ttu-id="fc869-781">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="fc869-782">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="fc869-783">Run</span><span class="sxs-lookup"><span data-stu-id="fc869-783">Run</span></span>

<span data-ttu-id="fc869-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="fc869-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-785">RunAsync</span></span>

<span data-ttu-id="fc869-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="fc869-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-787">RunConsoleAsync</span></span>

<span data-ttu-id="fc869-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="fc869-789">Start</span><span class="sxs-lookup"><span data-stu-id="fc869-789">Start</span></span>

<span data-ttu-id="fc869-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="fc869-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-791">StartAsync</span></span>

<span data-ttu-id="fc869-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="fc869-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="fc869-794">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="fc869-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-795">StopAsync</span></span>

<span data-ttu-id="fc869-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="fc869-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="fc869-797">WaitForShutdown</span></span>

<span data-ttu-id="fc869-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="fc869-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="fc869-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="fc869-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="fc869-801">외부 제어</span><span class="sxs-lookup"><span data-stu-id="fc869-801">External control</span></span>

<span data-ttu-id="fc869-802">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fc869-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fc869-803">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fc869-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
