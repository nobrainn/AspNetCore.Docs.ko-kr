---
<span data-ttu-id="58d6a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-102">'Blazor'</span></span>
- <span data-ttu-id="58d6a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-103">'Identity'</span></span>
- <span data-ttu-id="58d6a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-105">'Razor'</span></span>
- <span data-ttu-id="58d6a-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="58d6a-107">ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="58d6a-107">ASP.NET Core Module</span></span>

<span data-ttu-id="58d6a-108">작성자: [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="58d6a-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58d6a-109">ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="58d6a-110">IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="58d6a-111">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="58d6a-112">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="58d6a-112">Supported Windows versions:</span></span>

* <span data-ttu-id="58d6a-113">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-113">Windows 7 or later</span></span>
* <span data-ttu-id="58d6a-114">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="58d6a-115">In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="58d6a-116">Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="58d6a-117">이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="58d6a-118">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="58d6a-119">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-119">In-process hosting model</span></span>

<span data-ttu-id="58d6a-120">ASP.NET Core 앱의 기본값은 In-process 호스팅 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="58d6a-121">다음 특성은 In-Process로 호스팅할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="58d6a-122">IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="58d6a-123">In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="58d6a-124">`IISHttpServer`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="58d6a-125">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="58d6a-126">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="58d6a-127">[requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="58d6a-128">앱 간의 앱 풀 공유는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="58d6a-129">앱당 하나의 앱 풀을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-129">Use one app pool per app.</span></span>

* <span data-ttu-id="58d6a-130">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [app_offline.htm 파일을 배포에](xref:host-and-deploy/iis/index#locked-deployment-files) 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="58d6a-131">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="58d6a-132">앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="58d6a-133">클라이언트의 연결 끊김이 검색되었습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-133">Client disconnects are detected.</span></span> <span data-ttu-id="58d6a-134">클라이언트의 연결이 끊어지면 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="58d6a-135">ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: *w3wp.exe*에 대한 *C:\Windows\System32\inetsrv*).</span><span class="sxs-lookup"><span data-stu-id="58d6a-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="58d6a-136">앱의 현재 디렉터리를 설정하는 샘플 코드는 [CurrentDirectoryHelpers 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="58d6a-137">`SetCurrentDirectory` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="58d6a-138"><xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="58d6a-139">In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="58d6a-140">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="58d6a-141"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="58d6a-142">[웹 패키지(단일 파일) 배포](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="58d6a-143">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-143">Out-of-process hosting model</span></span>

<span data-ttu-id="58d6a-144">Out-of-Process 호스팅을 위해 앱을 구성하려면 프로젝트 파일( *.csproj*)에서 `<AspNetCoreHostingModel>` 속성의 값을 `OutOfProcess`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="58d6a-145">In-Process 호스팅은 기본값 `InProcess`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="58d6a-146">`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="58d6a-147">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="58d6a-148">Out of Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="58d6a-149">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="58d6a-150">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="58d6a-151">호스팅 모델 변경</span><span class="sxs-lookup"><span data-stu-id="58d6a-151">Hosting model changes</span></span>

<span data-ttu-id="58d6a-152">`hostingModel` 설정이 *web.config* 파일에서 변경되면([web.config로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="58d6a-153">IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="58d6a-154">앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="58d6a-155">프로세스 이름</span><span class="sxs-lookup"><span data-stu-id="58d6a-155">Process name</span></span>

<span data-ttu-id="58d6a-156">`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="58d6a-157">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="58d6a-158">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="58d6a-159">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="58d6a-160">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="58d6a-161">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="58d6a-162">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="58d6a-163">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="58d6a-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="58d6a-164">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="58d6a-165">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="58d6a-165">Configuration with web.config</span></span>

<span data-ttu-id="58d6a-166">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="58d6a-167">다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="58d6a-168">다음 *web.config*는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="58d6a-169"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="58d6a-170">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-171">이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="58d6a-172">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="58d6a-173">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="58d6a-174">특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-174">Attribute</span></span> | <span data-ttu-id="58d6a-175">설명</span><span class="sxs-lookup"><span data-stu-id="58d6a-175">Description</span></span> | <span data-ttu-id="58d6a-176">기본값</span><span class="sxs-lookup"><span data-stu-id="58d6a-176">Default</span></span> |
| ---
<span data-ttu-id="58d6a-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-178">'Blazor'</span></span>
- <span data-ttu-id="58d6a-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-179">'Identity'</span></span>
- <span data-ttu-id="58d6a-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-181">'Razor'</span></span>
- <span data-ttu-id="58d6a-182">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-184">'Blazor'</span></span>
- <span data-ttu-id="58d6a-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-185">'Identity'</span></span>
- <span data-ttu-id="58d6a-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-187">'Razor'</span></span>
- <span data-ttu-id="58d6a-188">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-188">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-190">'Blazor'</span></span>
- <span data-ttu-id="58d6a-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-191">'Identity'</span></span>
- <span data-ttu-id="58d6a-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-193">'Razor'</span></span>
- <span data-ttu-id="58d6a-194">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-196">'Blazor'</span></span>
- <span data-ttu-id="58d6a-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-197">'Identity'</span></span>
- <span data-ttu-id="58d6a-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-199">'Razor'</span></span>
- <span data-ttu-id="58d6a-200">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-202">'Blazor'</span></span>
- <span data-ttu-id="58d6a-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-203">'Identity'</span></span>
- <span data-ttu-id="58d6a-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-205">'Razor'</span></span>
- <span data-ttu-id="58d6a-206">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-206">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="58d6a-208">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-208">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-209">**processPath**에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="58d6a-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="58d6a-211">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-212">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config*에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="58d6a-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="58d6a-214">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-215">true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="58d6a-216">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="58d6a-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="58d6a-218">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-218">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-219">호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="58d6a-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="58d6a-221">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-222">앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="58d6a-223">&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="58d6a-224">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="58d6a-225">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="58d6a-226">| 기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-226">| Default: `1`</span></span><br><span data-ttu-id="58d6a-227">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-227">Min: `1`</span></span><br><span data-ttu-id="58d6a-228">최대: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="58d6a-229">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-229">Required string attribute.</span></span></p><p><span data-ttu-id="58d6a-230">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="58d6a-231">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-231">Relative paths are supported.</span></span> <span data-ttu-id="58d6a-232">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="58d6a-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="58d6a-234">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-235">**processPath**에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="58d6a-236">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="58d6a-237">In-Process 호스팅에서는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="58d6a-238">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-238">| Default: `10`</span></span><br><span data-ttu-id="58d6a-239">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-239">Min: `0`</span></span><br><span data-ttu-id="58d6a-240">최대: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="58d6a-241">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="58d6a-242">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="58d6a-243">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="58d6a-244">In-Process 호스팅에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="58d6a-245">In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="58d6a-246">문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="58d6a-247">분 또는 초의 값에 **60**을 사용하면 *500 - 내부 서버 오류*가 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="58d6a-248">| 기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="58d6a-249">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-249">Min: `00:00:00`</span></span><br><span data-ttu-id="58d6a-250">최대: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="58d6a-251">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-252">*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="58d6a-253">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-253">| Default: `10`</span></span><br><span data-ttu-id="58d6a-254">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-254">Min: `0`</span></span><br><span data-ttu-id="58d6a-255">최대: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="58d6a-256">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-257">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="58d6a-258">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="58d6a-259">모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute**번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="58d6a-260">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="58d6a-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="58d6a-261">| 기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="58d6a-261">| Default: `120`</span></span><br><span data-ttu-id="58d6a-262">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-262">Min: `0`</span></span><br><span data-ttu-id="58d6a-263">최대: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="58d6a-264">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-265">true인 경우 **processPath**에 지정된 프로세스에 대한 **stdout** 및 **stderr**이 **stdoutLogFile**에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="58d6a-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="58d6a-267">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-267">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-268">**processPath**에 지정된 프로세스에서 **stdout** 및 **stderr**이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="58d6a-269">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="58d6a-270">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="58d6a-271">경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-272">타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="58d6a-273">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 *logs* 폴더에 *stdout_20180205194132_1934.log*로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="58d6a-274">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="58d6a-274">Set environment variables</span></span>

<span data-ttu-id="58d6a-275">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="58d6a-276">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="58d6a-277">이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="58d6a-278">다음 예제에서는 *web.config*에서 두 가지 환경 변수를 설정합니다. `ASPNETCORE_ENVIRONMENT`는 앱 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="58d6a-279">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 *web.config* 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="58d6a-280">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="58d6a-281">*web.config*에서 환경을 직접 설정하는 대안으로 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="58d6a-282">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="58d6a-283">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="58d6a-284">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="58d6a-284">app_offline.htm</span></span>

<span data-ttu-id="58d6a-285">응용 프로그램의 루트 디렉터리에서 이름이 *app_offline.htm*인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="58d6a-286">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="58d6a-287">*app_offline.htm* 파일이 있는 동안 ASP.NET Core 모듈은 *app_offline.htm* 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="58d6a-288">*app_offline.htm* 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="58d6a-289">Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="58d6a-290">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="58d6a-291">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="58d6a-291">Start-up error page</span></span>

<span data-ttu-id="58d6a-292">In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="58d6a-293">ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-294">in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-295">out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-296">이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="58d6a-297">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="58d6a-298">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="58d6a-298">Log creation and redirection</span></span>

<span data-ttu-id="58d6a-299">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="58d6a-300">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-301">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="58d6a-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="58d6a-302">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="58d6a-303">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="58d6a-304">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="58d6a-305">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="58d6a-306">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="58d6a-307">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="58d6a-308">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="58d6a-309">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 *stdout*)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="58d6a-310">`stdoutLogFile` 경로가 *stdout*으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 *stdout_20180205194132_1934.log*입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="58d6a-311">`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="58d6a-312">시작 후에는 모든 추가 로그가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="58d6a-313">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="58d6a-314">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="58d6a-315">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-316">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="58d6a-317">로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="58d6a-318">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="58d6a-319">개선된 진단 로그</span><span class="sxs-lookup"><span data-stu-id="58d6a-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="58d6a-320">ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="58d6a-321">*web.config*의 `<aspNetCore>` 요소에 `<handlerSettings>` 요소를 추가합니다. `debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="58d6a-322">경로에 있는 모든 폴더(위 예제의 *logs*)가 로그 파일을 만들 때 모듈에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-323">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="58d6a-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="58d6a-324">디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="58d6a-325">수준(최소한에서 가장 자세한 정보까지 순서대로 ):</span><span class="sxs-lookup"><span data-stu-id="58d6a-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="58d6a-326">오류</span><span class="sxs-lookup"><span data-stu-id="58d6a-326">ERROR</span></span>
* <span data-ttu-id="58d6a-327">경고</span><span class="sxs-lookup"><span data-stu-id="58d6a-327">WARNING</span></span>
* <span data-ttu-id="58d6a-328">정보</span><span class="sxs-lookup"><span data-stu-id="58d6a-328">INFO</span></span>
* <span data-ttu-id="58d6a-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="58d6a-329">TRACE</span></span>

<span data-ttu-id="58d6a-330">위치(여러 위치가 허용됨):</span><span class="sxs-lookup"><span data-stu-id="58d6a-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="58d6a-331">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="58d6a-331">CONSOLE</span></span>
* <span data-ttu-id="58d6a-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="58d6a-332">EVENTLOG</span></span>
* <span data-ttu-id="58d6a-333">FILE</span><span class="sxs-lookup"><span data-stu-id="58d6a-333">FILE</span></span>

<span data-ttu-id="58d6a-334">처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="58d6a-335">`ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="58d6a-336">(기본값: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="58d6a-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="58d6a-337">`ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="58d6a-338">배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="58d6a-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="58d6a-339">로그의 크기는 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-339">The size of the log isn't limited.</span></span> <span data-ttu-id="58d6a-340">디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="58d6a-341">*web.config* 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="58d6a-342">스택 크기 수정</span><span class="sxs-lookup"><span data-stu-id="58d6a-342">Modify the stack size</span></span>

<span data-ttu-id="58d6a-343">*In-process 호스팅 모델을 사용하는 경우에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="58d6a-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="58d6a-344">*web.config*에서 바이트 단위의 `stackSize` 설정을 사용하여 관리형 스택 크기를 구성합니다. 기본 크기는 `1048576`바이트(1MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="58d6a-345">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="58d6a-346">*호스팅에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="58d6a-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="58d6a-347">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="58d6a-348">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="58d6a-349">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="58d6a-350">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="58d6a-351">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="58d6a-352">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="58d6a-353">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="58d6a-354">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="58d6a-355">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="58d6a-356">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="58d6a-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="58d6a-357">ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="58d6a-358">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 *applicationHost.config*에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="58d6a-359">IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다</span><span class="sxs-lookup"><span data-stu-id="58d6a-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="58d6a-360">공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="58d6a-361">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="58d6a-362">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-362">Run the installer.</span></span>
1. <span data-ttu-id="58d6a-363">업데이트된 *applicationHost.config* 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="58d6a-364">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="58d6a-365">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="58d6a-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="58d6a-366">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="58d6a-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="58d6a-367">호스팅 시스템에서 *%windir%\System32\inetsrv*로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="58d6a-368">*aspnetcore.dll* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="58d6a-369">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="58d6a-370">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전**은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="58d6a-371">모듈에 대한 호스팅 번들 설치 관리자 로그는 *C:\\Users\\%UserName%\\AppData\\Local\\Temp*에 있습니다. 파일 이름은 *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="58d6a-372">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="58d6a-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="58d6a-373">Module</span><span class="sxs-lookup"><span data-stu-id="58d6a-373">Module</span></span>

<span data-ttu-id="58d6a-374">**IIS(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="58d6a-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="58d6a-379">**IIS Express(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="58d6a-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="58d6a-384">스키마</span><span class="sxs-lookup"><span data-stu-id="58d6a-384">Schema</span></span>

<span data-ttu-id="58d6a-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-385">**IIS**</span></span>

* <span data-ttu-id="58d6a-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="58d6a-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="58d6a-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-388">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="58d6a-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="58d6a-391">Configuration</span><span class="sxs-lookup"><span data-stu-id="58d6a-391">Configuration</span></span>

<span data-ttu-id="58d6a-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-392">**IIS**</span></span>

* <span data-ttu-id="58d6a-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="58d6a-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-394">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="58d6a-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="58d6a-397">*applicationHost.config* 파일에서 *aspnetcore*를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="58d6a-398">ASP.NET Core 모듈은 다음을 위해 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="58d6a-399">IIS 작업자 프로세스(`w3wp.exe`) 내부에 ASP.NET Core 앱을 호스트하며 [In-Process 호스팅 모델](#in-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="58d6a-400">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 실행하는 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하며 [Out-of-Process 호스팅 모델](#out-of-process-hosting-model)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="58d6a-401">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="58d6a-401">Supported Windows versions:</span></span>

* <span data-ttu-id="58d6a-402">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-402">Windows 7 or later</span></span>
* <span data-ttu-id="58d6a-403">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="58d6a-404">In Process를 호스트하는 경우 모듈에서는 IIS HTTP Server(`IISHttpServer`)라는 IIS용 In Process 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="58d6a-405">Out-of-Process로 호스트하는 경우 모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="58d6a-406">이 모듈은 [HTTP.sys](xref:fundamentals/servers/httpsys)와 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="58d6a-407">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="58d6a-408">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-408">In-process hosting model</span></span>

<span data-ttu-id="58d6a-409">In-Process 호스팅용 앱을 구성하려면 `<AspNetCoreHostingModel>` 속성을 `InProcess` 값의 앱 프로젝트 파일에 추가합니다(Out-of-Process 호스팅은 `OutOfProcess`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="58d6a-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="58d6a-410">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="58d6a-411">`<AspNetCoreHostingModel>`의 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="58d6a-412">파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="58d6a-413">다음 특성은 In-Process로 호스팅할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="58d6a-414">IIS HTTP 서버(`IISHttpServer`)는 [Kestrel](xref:fundamentals/servers/kestrel) 서버 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="58d6a-415">In Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="58d6a-416">`IISHttpServer`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="58d6a-417">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="58d6a-418">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="58d6a-419">[requestTimeout 특성](#attributes-of-the-aspnetcore-element)이 In-Process 호스팅에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="58d6a-420">앱 간의 앱 풀 공유는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="58d6a-421">앱당 하나의 앱 풀을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-421">Use one app pool per app.</span></span>

* <span data-ttu-id="58d6a-422">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)를 사용하거나 [app_offline.htm 파일을 배포에](xref:host-and-deploy/iis/index#locked-deployment-files) 수동으로 배치할 경우 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="58d6a-423">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="58d6a-424">앱 및 설치된 런타임(x64 또는 x86)의 아키텍처(비트)는 앱 풀의 아키텍처와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="58d6a-425">클라이언트의 연결 끊김이 검색되었습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-425">Client disconnects are detected.</span></span> <span data-ttu-id="58d6a-426">클라이언트의 연결이 끊어지면 [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) 취소 토큰이 취소됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="58d6a-427">ASP.NET Core 2.2.1 이하에서 <xref:System.IO.Directory.GetCurrentDirectory*>는 앱 디렉터리가 아닌 IIS에 의해 시작된 프로세스의 작업자 디렉터리를 반환합니다(예: *w3wp.exe*에 대한 *C:\Windows\System32\inetsrv*).</span><span class="sxs-lookup"><span data-stu-id="58d6a-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="58d6a-428">앱의 현재 디렉터리를 설정하는 샘플 코드는 [CurrentDirectoryHelpers 클래스](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="58d6a-429">`SetCurrentDirectory` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="58d6a-430"><xref:System.IO.Directory.GetCurrentDirectory*>에 대한 후속 호출은 앱의 디렉터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="58d6a-431">In-process로 호스팅하는 경우 사용자를 초기화하기 위해 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>를 내부적으로 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="58d6a-432">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="58d6a-433"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현으로 클레임을 변환할 때 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>을 호출하여 인증 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="58d6a-434">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="58d6a-434">Out-of-process hosting model</span></span>

<span data-ttu-id="58d6a-435">Out of Process 호스팅을 위한 앱을 구성하려면 프로젝트 파일에서 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="58d6a-436">`<AspNetCoreHostingModel>` 속성을 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="58d6a-437">파일에 `<AspNetCoreHostingModel>` 속성이 없으면 기본값은 `OutOfProcess`입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="58d6a-438">`<AspNetCoreHostingModel>` 속성 값을 `OutOfProcess`로 설정합니다(In Process 호스트팅은 `InProcess`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="58d6a-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="58d6a-439">이 값은 대/소문자를 구분하지 않으므로 `inprocess`와 `outofprocess`는 유효한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="58d6a-440">[Kestrel](xref:fundamentals/servers/kestrel) 서버는 IIS HTTP 서버(`IISHttpServer`) 대신 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="58d6a-441">Out of Process의 경우 [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>를 호출하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="58d6a-442">ASP.NET Core 모듈 뒤에서 실행될 때 서버가 수신 대기해야 하는 포트 및 기본 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="58d6a-443">시작 오류를 캡처하도록 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="58d6a-444">호스팅 모델 변경</span><span class="sxs-lookup"><span data-stu-id="58d6a-444">Hosting model changes</span></span>

<span data-ttu-id="58d6a-445">`hostingModel` 설정이 *web.config* 파일에서 변경되면([web.config로 구성](#configuration-with-webconfig) 섹션에 설명되어 있음) 모듈은 IIS에 대한 작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="58d6a-446">IIS Express의 경우 모듈은 작업자 프로세스를 재순환하지 않고, 대신 현재 IIS Express 프로세스의 정상 종료를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="58d6a-447">앱에 대한 다음 요청은 새 IIS Express 프로세스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="58d6a-448">프로세스 이름</span><span class="sxs-lookup"><span data-stu-id="58d6a-448">Process name</span></span>

<span data-ttu-id="58d6a-449">`Process.GetCurrentProcess().ProcessName`은 `w3wp`/`iisexpress`(In-Process) 또는 `dotnet`(Out-of-Process)을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="58d6a-450">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="58d6a-451">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="58d6a-452">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="58d6a-453">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="58d6a-454">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="58d6a-455">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="58d6a-456">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="58d6a-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="58d6a-457">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="58d6a-458">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="58d6a-458">Configuration with web.config</span></span>

<span data-ttu-id="58d6a-459">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="58d6a-460">다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="58d6a-461">다음 *web.config*는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="58d6a-462"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="58d6a-463">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-464">이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="58d6a-465">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="58d6a-466">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="58d6a-467">특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-467">Attribute</span></span> | <span data-ttu-id="58d6a-468">설명</span><span class="sxs-lookup"><span data-stu-id="58d6a-468">Description</span></span> | <span data-ttu-id="58d6a-469">기본값</span><span class="sxs-lookup"><span data-stu-id="58d6a-469">Default</span></span> |
| ---
<span data-ttu-id="58d6a-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-471">'Blazor'</span></span>
- <span data-ttu-id="58d6a-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-472">'Identity'</span></span>
- <span data-ttu-id="58d6a-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-474">'Razor'</span></span>
- <span data-ttu-id="58d6a-475">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-477">'Blazor'</span></span>
- <span data-ttu-id="58d6a-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-478">'Identity'</span></span>
- <span data-ttu-id="58d6a-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-480">'Razor'</span></span>
- <span data-ttu-id="58d6a-481">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-481">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-483">'Blazor'</span></span>
- <span data-ttu-id="58d6a-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-484">'Identity'</span></span>
- <span data-ttu-id="58d6a-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-486">'Razor'</span></span>
- <span data-ttu-id="58d6a-487">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-489">'Blazor'</span></span>
- <span data-ttu-id="58d6a-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-490">'Identity'</span></span>
- <span data-ttu-id="58d6a-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-492">'Razor'</span></span>
- <span data-ttu-id="58d6a-493">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-495">'Blazor'</span></span>
- <span data-ttu-id="58d6a-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-496">'Identity'</span></span>
- <span data-ttu-id="58d6a-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-498">'Razor'</span></span>
- <span data-ttu-id="58d6a-499">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-499">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="58d6a-501">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-501">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-502">**processPath**에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="58d6a-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="58d6a-504">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-505">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config*에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="58d6a-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="58d6a-507">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-508">true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="58d6a-509">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="58d6a-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="58d6a-511">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-511">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-512">호스팅 모델을 In-Process(`InProcess`/`inprocess`) 또는 Out-of-Process(`OutOfProcess`/`outofprocess`)로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="58d6a-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="58d6a-514">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-515">앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="58d6a-516">&dagger;In-Process 호스팅의 경우 이 값은 `1`로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="58d6a-517">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="58d6a-518">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="58d6a-519">| 기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-519">| Default: `1`</span></span><br><span data-ttu-id="58d6a-520">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-520">Min: `1`</span></span><br><span data-ttu-id="58d6a-521">최대: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="58d6a-522">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-522">Required string attribute.</span></span></p><p><span data-ttu-id="58d6a-523">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="58d6a-524">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-524">Relative paths are supported.</span></span> <span data-ttu-id="58d6a-525">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="58d6a-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="58d6a-527">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-528">**processPath**에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="58d6a-529">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="58d6a-530">In-Process 호스팅에서는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="58d6a-531">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-531">| Default: `10`</span></span><br><span data-ttu-id="58d6a-532">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-532">Min: `0`</span></span><br><span data-ttu-id="58d6a-533">최대: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="58d6a-534">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="58d6a-535">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="58d6a-536">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="58d6a-537">In-Process 호스팅에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="58d6a-538">In-Process 호스팅의 경우 모듈은 앱이 요청을 처리할 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="58d6a-539">문자열의 분 및 초 세그먼트에 유효한 값은 0-59 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="58d6a-540">분 또는 초의 값에 **60**을 사용하면 *500 - 내부 서버 오류*가 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="58d6a-541">| 기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="58d6a-542">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-542">Min: `00:00:00`</span></span><br><span data-ttu-id="58d6a-543">최대: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="58d6a-544">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-545">*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="58d6a-546">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-546">| Default: `10`</span></span><br><span data-ttu-id="58d6a-547">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-547">Min: `0`</span></span><br><span data-ttu-id="58d6a-548">최대: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="58d6a-549">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-550">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="58d6a-551">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="58d6a-552">모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute**번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="58d6a-553">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="58d6a-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="58d6a-554">| 기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="58d6a-554">| Default: `120`</span></span><br><span data-ttu-id="58d6a-555">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-555">Min: `0`</span></span><br><span data-ttu-id="58d6a-556">최대: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="58d6a-557">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-558">true인 경우 **processPath**에 지정된 프로세스에 대한 **stdout** 및 **stderr**이 **stdoutLogFile**에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="58d6a-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="58d6a-560">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-560">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-561">**processPath**에 지정된 프로세스에서 **stdout** 및 **stderr**이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="58d6a-562">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="58d6a-563">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="58d6a-564">경로에 제공된 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-565">타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="58d6a-566">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 *logs* 폴더에 *stdout_20180205194132_1934.log*로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="58d6a-567">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="58d6a-567">Setting environment variables</span></span>

<span data-ttu-id="58d6a-568">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="58d6a-569">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="58d6a-570">이 섹션에 설정된 환경 변수가 시스템 환경 변수보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="58d6a-571">다음 예제에서는 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-571">The following example sets two environment variables.</span></span> <span data-ttu-id="58d6a-572">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="58d6a-573">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 *web.config* 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="58d6a-574">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="58d6a-575">*web.config*에서 환경을 직접 설정하는 대안으로 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 `<EnvironmentName>` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="58d6a-576">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="58d6a-577">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="58d6a-578">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="58d6a-578">app_offline.htm</span></span>

<span data-ttu-id="58d6a-579">응용 프로그램의 루트 디렉터리에서 이름이 *app_offline.htm*인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="58d6a-580">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="58d6a-581">*app_offline.htm* 파일이 있는 동안 ASP.NET Core 모듈은 *app_offline.htm* 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="58d6a-582">*app_offline.htm* 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="58d6a-583">Out-of-Process 호스팅 모델을 사용할 때 열린 연결이 있으면 앱이 즉시 종료되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="58d6a-584">예를 들어, WebSocket 연결은 앱 종료를 지연시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="58d6a-585">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="58d6a-585">Start-up error page</span></span>

<span data-ttu-id="58d6a-586">In process 및 out of process 호스팅은 모두 앱을 시작하지 못할 때 사용자 지정 오류 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="58d6a-587">ASP.NET Core 모듈이 in-process 또는 out-of-process 요청 처리기를 찾지 못하면 *500.0 - In-Process/Out-of-process 처리기 로드 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-588">in-process 호스팅의 경우 ASP.NET Core 모듈이 앱을 시작하지 못하면 *500.30 - 시작 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-589">out-of-process 호스팅의 경우 ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="58d6a-590">이 페이지를 표시하지 않고 기본 IIS 5xx 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="58d6a-591">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="58d6a-592">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="58d6a-592">Log creation and redirection</span></span>

<span data-ttu-id="58d6a-593">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="58d6a-594">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-595">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="58d6a-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="58d6a-596">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="58d6a-597">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="58d6a-598">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="58d6a-599">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="58d6a-600">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="58d6a-601">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="58d6a-602">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="58d6a-603">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 *stdout*)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="58d6a-604">`stdoutLogFile` 경로가 *stdout*으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 *stdout_20180205194132_1934.log*입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="58d6a-605">`stdoutLogEnabled`가 false이면 앱 시작 시 발생하는 오류가 캡처되어 최대 30KB의 이벤트 로그로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="58d6a-606">시작 후에는 모든 추가 로그가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="58d6a-607">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="58d6a-608">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="58d6a-609">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-610">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="58d6a-611">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="58d6a-612">개선된 진단 로그</span><span class="sxs-lookup"><span data-stu-id="58d6a-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="58d6a-613">ASP.NET Core 모듈은 개선된 진단 로그를 제공하도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="58d6a-614">*web.config*의 `<aspNetCore>` 요소에 `<handlerSettings>` 요소를 추가합니다. `debugLevel`을 `TRACE`으로 설정하면 진단 정보의 충실도가 높아집니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="58d6a-615">`<handlerSetting>` 값에 제공한 경로에 있는 폴더(위 예제의 *logs*)가 모듈에서 자동으로 생성되지 않으며, 배포에 있는 기존 폴더여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="58d6a-616">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="58d6a-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="58d6a-617">디버그 수준 (`debugLevel`) 값은 수준과 위치를 모두 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="58d6a-618">수준(최소한에서 가장 자세한 정보까지 순서대로 ):</span><span class="sxs-lookup"><span data-stu-id="58d6a-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="58d6a-619">오류</span><span class="sxs-lookup"><span data-stu-id="58d6a-619">ERROR</span></span>
* <span data-ttu-id="58d6a-620">경고</span><span class="sxs-lookup"><span data-stu-id="58d6a-620">WARNING</span></span>
* <span data-ttu-id="58d6a-621">정보</span><span class="sxs-lookup"><span data-stu-id="58d6a-621">INFO</span></span>
* <span data-ttu-id="58d6a-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="58d6a-622">TRACE</span></span>

<span data-ttu-id="58d6a-623">위치(여러 위치가 허용됨):</span><span class="sxs-lookup"><span data-stu-id="58d6a-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="58d6a-624">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="58d6a-624">CONSOLE</span></span>
* <span data-ttu-id="58d6a-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="58d6a-625">EVENTLOG</span></span>
* <span data-ttu-id="58d6a-626">FILE</span><span class="sxs-lookup"><span data-stu-id="58d6a-626">FILE</span></span>

<span data-ttu-id="58d6a-627">처리기 설정은 환경 변수를 통해서도 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="58d6a-628">`ASPNETCORE_MODULE_DEBUG_FILE`: 디버그 로그 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="58d6a-629">(기본값: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="58d6a-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="58d6a-630">`ASPNETCORE_MODULE_DEBUG`: 디버그 수준 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="58d6a-631">배포에서 문제를 해결하는 데 필요한 시간보다 오래 디버그 로깅을 사용하도록 설정하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="58d6a-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="58d6a-632">로그의 크기는 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-632">The size of the log isn't limited.</span></span> <span data-ttu-id="58d6a-633">디버그 로그를 사용하도록 설정한 대로 두면 사용 가능한 디스크 공간이 소진되어 서버 또는 앱 서비스가 크래시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="58d6a-634">*web.config* 파일에 있는 `aspNetCore` 요소의 예제에 대해서는 [web.config를 사용한 구성](#configuration-with-webconfig)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="58d6a-635">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="58d6a-636">*호스팅에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="58d6a-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="58d6a-637">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="58d6a-638">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="58d6a-639">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="58d6a-640">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="58d6a-641">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="58d6a-642">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="58d6a-643">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="58d6a-644">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="58d6a-645">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="58d6a-646">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="58d6a-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="58d6a-647">ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="58d6a-648">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 *applicationHost.config*에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="58d6a-649">IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다</span><span class="sxs-lookup"><span data-stu-id="58d6a-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="58d6a-650">공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="58d6a-651">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="58d6a-652">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-652">Run the installer.</span></span>
1. <span data-ttu-id="58d6a-653">업데이트된 *applicationHost.config* 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="58d6a-654">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="58d6a-655">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="58d6a-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="58d6a-656">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="58d6a-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="58d6a-657">호스팅 시스템에서 *%windir%\System32\inetsrv*로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="58d6a-658">*aspnetcore.dll* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="58d6a-659">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="58d6a-660">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전**은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="58d6a-661">모듈에 대한 호스팅 번들 설치 관리자 로그는 *C:\\Users\\%UserName%\\AppData\\Local\\Temp*에 있습니다. 파일 이름은 *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="58d6a-662">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="58d6a-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="58d6a-663">Module</span><span class="sxs-lookup"><span data-stu-id="58d6a-663">Module</span></span>

<span data-ttu-id="58d6a-664">**IIS(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="58d6a-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="58d6a-669">**IIS Express(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="58d6a-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="58d6a-674">스키마</span><span class="sxs-lookup"><span data-stu-id="58d6a-674">Schema</span></span>

<span data-ttu-id="58d6a-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-675">**IIS**</span></span>

* <span data-ttu-id="58d6a-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="58d6a-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="58d6a-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-678">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="58d6a-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="58d6a-681">Configuration</span><span class="sxs-lookup"><span data-stu-id="58d6a-681">Configuration</span></span>

<span data-ttu-id="58d6a-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-682">**IIS**</span></span>

* <span data-ttu-id="58d6a-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="58d6a-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-684">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="58d6a-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="58d6a-687">*applicationHost.config* 파일에서 *aspnetcore*를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="58d6a-688">ASP.NET Core 모듈은 백 엔드 ASP.NET Core 앱으로 웹 요청을 전달하는 IIS 파이프라인에 연결되는 네이티브 IIS 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="58d6a-689">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="58d6a-689">Supported Windows versions:</span></span>

* <span data-ttu-id="58d6a-690">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-690">Windows 7 or later</span></span>
* <span data-ttu-id="58d6a-691">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="58d6a-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="58d6a-692">모듈은 Kestrel에서만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-692">The module only works with Kestrel.</span></span> <span data-ttu-id="58d6a-693">모듈이 [HTTP.sys](xref:fundamentals/servers/httpsys)와 호환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="58d6a-694">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리도 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="58d6a-695">이 모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="58d6a-696">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 IIS에서 In Process로 실행되는 ASP.NET 4.x 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="58d6a-697">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core 모듈](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="58d6a-699">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="58d6a-700">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="58d6a-701">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="58d6a-702">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="58d6a-703">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="58d6a-704">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="58d6a-705">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="58d6a-706">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="58d6a-707">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="58d6a-708">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="58d6a-709">Windows 인증 등의 많은 네이티브 모듈이 활성 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="58d6a-710">ASP.NET Core 모듈을 사용하여 활성화된 IIS 모듈에 대해 자세히 알아보려면 <xref:host-and-deploy/iis/modules>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="58d6a-711">ASP.NET Core 모듈은 다음 작업을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="58d6a-712">작업자 프로세스의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="58d6a-713">시작 문제를 해결하기 위해 stdout 출력을 파일 스토리지에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="58d6a-714">Windows 인증 토큰을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="58d6a-715">ASP.NET Core 모듈을 설치하고 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="58d6a-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="58d6a-716">ASP.NET Core 모듈을 설치하는 방법에 대한 지침은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="58d6a-717">web.config를 사용한 구성</span><span class="sxs-lookup"><span data-stu-id="58d6a-717">Configuration with web.config</span></span>

<span data-ttu-id="58d6a-718">ASP.NET Core 모듈은 사이트의 *web.config* 파일에 있는 `system.webServer` 노드의 `aspNetCore` 섹션으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="58d6a-719">다음 *web.config* 파일은 [프레임워크 종속 배포](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)를 위해 게시되고 사이트 요청을 처리하도록 ASP.NET Core 모듈을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="58d6a-720">다음 *web.config*는 [자체 포함 배포](/dotnet/articles/core/deploying/#self-contained-deployments-scd)를 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="58d6a-721">앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포되면 `stdoutLogFile` 경로가 `\\?\%home%\LogFiles\stdout`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-722">이 경로는 서비스에서 자동으로 만들어진 위치인 *LogFiles* 폴더에 stdout 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="58d6a-723">IIS 하위 애플리케이션 구성에 대한 자세한 내용은 <xref:host-and-deploy/iis/index#sub-applications>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="58d6a-724">aspNetCore 요소의 특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="58d6a-725">특성</span><span class="sxs-lookup"><span data-stu-id="58d6a-725">Attribute</span></span> | <span data-ttu-id="58d6a-726">설명</span><span class="sxs-lookup"><span data-stu-id="58d6a-726">Description</span></span> | <span data-ttu-id="58d6a-727">기본값</span><span class="sxs-lookup"><span data-stu-id="58d6a-727">Default</span></span> |
| ---
<span data-ttu-id="58d6a-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-729">'Blazor'</span></span>
- <span data-ttu-id="58d6a-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-730">'Identity'</span></span>
- <span data-ttu-id="58d6a-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-732">'Razor'</span></span>
- <span data-ttu-id="58d6a-733">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-735">'Blazor'</span></span>
- <span data-ttu-id="58d6a-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-736">'Identity'</span></span>
- <span data-ttu-id="58d6a-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-738">'Razor'</span></span>
- <span data-ttu-id="58d6a-739">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-739">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-741">'Blazor'</span></span>
- <span data-ttu-id="58d6a-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-742">'Identity'</span></span>
- <span data-ttu-id="58d6a-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-744">'Razor'</span></span>
- <span data-ttu-id="58d6a-745">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-747">'Blazor'</span></span>
- <span data-ttu-id="58d6a-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-748">'Identity'</span></span>
- <span data-ttu-id="58d6a-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-750">'Razor'</span></span>
- <span data-ttu-id="58d6a-751">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="58d6a-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58d6a-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="58d6a-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-753">'Blazor'</span></span>
- <span data-ttu-id="58d6a-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58d6a-754">'Identity'</span></span>
- <span data-ttu-id="58d6a-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58d6a-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="58d6a-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58d6a-756">'Razor'</span></span>
- <span data-ttu-id="58d6a-757">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="58d6a-757">'SignalR' uid:</span></span> 

<span data-ttu-id="58d6a-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="58d6a-759">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-759">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-760">**processPath**에 지정된 실행 파일에 대한 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="58d6a-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="58d6a-762">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-763">true인 경우 **502.5 - 프로세스 실패** 페이지가 표시되지 않고 *web.config*에 구성된 502 상태 코드 페이지가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="58d6a-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="58d6a-765">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-766">true인 경우 토큰은 %ASPNETCORE_PORT%에서 수신 대기하는 자식 프로세스에 요청별 헤더 'MS-ASPNETCORE-WINAUTHTOKEN'으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="58d6a-767">이 프로세스는 요청별로 이 토큰에서 CloseHandle을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="58d6a-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="58d6a-769">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-770">앱별로 스핀 업할 수 있는 **processPath** 설정에 지정된 프로세스의 인스턴스 수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="58d6a-771">설정 `processesPerApplication`은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="58d6a-772">이 특성은 이후 릴리스에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="58d6a-773">| 기본값: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-773">| Default: `1`</span></span><br><span data-ttu-id="58d6a-774">최소: `1`</span><span class="sxs-lookup"><span data-stu-id="58d6a-774">Min: `1`</span></span><br><span data-ttu-id="58d6a-775">최대: `100` | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="58d6a-776">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-776">Required string attribute.</span></span></p><p><span data-ttu-id="58d6a-777">HTTP 요청을 수신 대기하는 프로세스를 시작하는 실행 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="58d6a-778">상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-778">Relative paths are supported.</span></span> <span data-ttu-id="58d6a-779">경로가 `.`로 시작되면 경로는 사이트 루트의 상대 경로로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="58d6a-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="58d6a-781">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-782">**processPath**에 지정된 프로세스의 분당 크래시 허용 횟수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="58d6a-783">이 제한을 초과하면 모듈은 남은 시간 동안 프로세스 시작을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="58d6a-784">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-784">| Default: `10`</span></span><br><span data-ttu-id="58d6a-785">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-785">Min: `0`</span></span><br><span data-ttu-id="58d6a-786">최대: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="58d6a-787">선택적 시간 간격 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="58d6a-788">ASP.NET Core 모듈이 %ASPNETCORE_PORT%에서 수신 대기하는 프로세스의 응답을 기다리는 기간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="58d6a-789">ASP.NET Core 2.1 이상 릴리스와 함께 제공되는 ASP.NET Core 모듈 버전에서는 `requestTimeout`이 전체 시간, 분, 초로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="58d6a-790">| 기본값: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="58d6a-791">최소: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="58d6a-791">Min: `00:00:00`</span></span><br><span data-ttu-id="58d6a-792">최대: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="58d6a-793">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-794">*app_offline.htm* 파일이 검색될 때 실행 파일이 정상적으로 종료될 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="58d6a-795">| 기본값: `10`</span><span class="sxs-lookup"><span data-stu-id="58d6a-795">| Default: `10`</span></span><br><span data-ttu-id="58d6a-796">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-796">Min: `0`</span></span><br><span data-ttu-id="58d6a-797">최대: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="58d6a-798">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="58d6a-799">실행 파일이 포트에서 수신 대기하는 프로세스를 시작할 때까지 모듈이 기다리는 기간(초)입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="58d6a-800">이 시간 제한을 초과하면 모듈이 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="58d6a-801">모듈은 새 요청을 수신할 때 프로세스를 다시 시작하려고 하고, 마지막 롤링 기간(분)에 앱이 **rapidFailsPerMinute**번 시작에 실패한 경우가 아니면 이후 요청이 들어올 때 프로세스를 계속 다시 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="58d6a-802">값 0은 무한 시간 제한으로 간주되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="58d6a-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="58d6a-803">| 기본값: `120`</span><span class="sxs-lookup"><span data-stu-id="58d6a-803">| Default: `120`</span></span><br><span data-ttu-id="58d6a-804">최소: `0`</span><span class="sxs-lookup"><span data-stu-id="58d6a-804">Min: `0`</span></span><br><span data-ttu-id="58d6a-805">최대: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="58d6a-806">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="58d6a-807">true인 경우 **processPath**에 지정된 프로세스에 대한 **stdout** 및 **stderr**이 **stdoutLogFile**에 지정된 파일로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="58d6a-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="58d6a-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="58d6a-809">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-809">Optional string attribute.</span></span></p><p><span data-ttu-id="58d6a-810">**processPath**에 지정된 프로세스에서 **stdout** 및 **stderr**이 기록되는 상대 또는 절대 파일 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="58d6a-811">상대 경로는 사이트 루트에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="58d6a-812">`.`로 시작하는 모든 경로는 사이트 루트에 상대적인 경로이고 다른 모든 경로는 절대 경로로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="58d6a-813">모듈이 로그 파일을 만들려면 경로에 제공된 모든 폴더가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="58d6a-814">타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)은 밑줄 구분 기호를 사용하여 **stdoutLogFile** 경로의 마지막 세그먼트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="58d6a-815">`.\logs\stdout`이 값으로 제공되는 경우 예제 stdout 로그는 2018년 2월 5일 19시 41분 32초에 프로세스 ID 1934를 사용하여 저장될 경우 *logs* 폴더에 *stdout_20180205194132_1934.log*로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="58d6a-816">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="58d6a-816">Setting environment variables</span></span>

<span data-ttu-id="58d6a-817">`processPath` 특성에서 프로세스에 대한 환경 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="58d6a-818">`<environmentVariables>` 컬렉션 요소의 `<environmentVariable>` 자식 요소를 사용하여 환경 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="58d6a-819">이 섹션에 설정된 환경 변수가 동일한 이름으로 설정된 시스템 환경 변수와 충돌합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="58d6a-820">환경 변수가 *web.config* 파일과 Windows의 시스템 수준에 모두 설정된 경우 *web.config* 파일의 값은 시스템 환경 변수 값(예: `ASPNETCORE_ENVIRONMENT: Development;Development`)에 추가되어 앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="58d6a-821">다음 예제에서는 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-821">The following example sets two environment variables.</span></span> <span data-ttu-id="58d6a-822">`ASPNETCORE_ENVIRONMENT`는 앱의 환경을 `Development`로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="58d6a-823">앱 예외를 디버그할 때 [개발자 예외 페이지](xref:fundamentals/error-handling)를 강제로 로드하기 위해 개발자가 *web.config* 파일에서 이 값을 일시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="58d6a-824">`CONFIG_DIR`은 개발자가 앱 구성 파일을 로드할 경로를 생성하기 위해 시작 시 값을 읽는 코드를 작성한 사용자 정의 환경 변수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="58d6a-825">인터넷과 같은 신뢰할 수 없는 네트워크에 액세스할 수 없는 스테이징 및 테스트 서버에서는 `ASPNETCORE_ENVIRONMENT` 환경 변수를 `Development`로 설정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="58d6a-826">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="58d6a-826">app_offline.htm</span></span>

<span data-ttu-id="58d6a-827">응용 프로그램의 루트 디렉터리에서 이름이 *app_offline.htm*인 파일이 검색되면 ASP.NET Core 모듈은 앱을 자동으로 종료하고 들어오는 요청 처리를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="58d6a-828">`shutdownTimeLimit`에 정의된 시간(초) 후에도 앱이 계속 실행되면 ASP.NET Core 모듈은 실행 중인 프로세스를 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="58d6a-829">*app_offline.htm* 파일이 있는 동안 ASP.NET Core 모듈은 *app_offline.htm* 파일의 콘텐츠를 다시 보내 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="58d6a-830">*app_offline.htm* 파일이 제거되면 다음 요청이 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="58d6a-831">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="58d6a-831">Start-up error page</span></span>

<span data-ttu-id="58d6a-832">ASP.NET Core 모듈이 백 엔드 프로세스를 시작하지 못하거나 백 엔드 프로세스가 시작되지만 구성된 포트에서 수신 대기하지 못하면 *502.5 - 프로세스 실패* 상태 코드 페이지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="58d6a-833">이 페이지를 표시하지 않고 기본 IIS 502 상태 코드 페이지로 되돌리려면 `disableStartUpErrorPage` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="58d6a-834">사용자 지정 오류 메시지 구성에 대한 자세한 내용은 [HTTP 오류\<httpErrors>](/iis/configuration/system.webServer/httpErrors/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 프로세스 실패 상태 코드 페이지](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="58d6a-836">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="58d6a-836">Log creation and redirection</span></span>

<span data-ttu-id="58d6a-837">ASP.NET Core 모듈은 `aspNetCore` 요소의 `stdoutLogEnabled` 및 `stdoutLogFile` 특성이 설정된 경우 stdout 및 stderr 콘솔 출력을 디스크로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="58d6a-838">`stdoutLogFile` 경로의 모든 폴더는 로그 파일을 만들 때 모듈에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="58d6a-839">앱 풀에는 로그가 기록될 위치에 쓰기 권한이 있어야 합니다(`IIS AppPool\<app_pool_name>`을 사용하여 쓰기 권한 제공).</span><span class="sxs-lookup"><span data-stu-id="58d6a-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="58d6a-840">프로세스 재생/다시 시작이 발생하지 않는 한 로그는 회전되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="58d6a-841">로그에서 사용하는 디스크 공간을 제한하는 것은 호스터의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="58d6a-842">stdout 로그는 IIS에서 호스팅할 때 또는 [Visual Studio에서 IIS를 위한 개발 시간 지원](xref:host-and-deploy/iis/development-time-iis-support)을 사용할 때 앱 시작 문제를 해결하기 위해서만 사용하는 것이 좋으며, 로컬에서 디버깅하면서 IIS Express를 사용하여 앱을 실행할 때는 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="58d6a-843">일반 앱 로깅을 위해 stdout 로그를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="58d6a-844">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="58d6a-845">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="58d6a-846">로그 파일이 만들어질 때 타임스탬프 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="58d6a-847">로그 파일 이름은 타임스탬프, 프로세스 ID 및 파일 확장명( *.log*)을 밑줄로 구분된 `stdoutLogFile` 경로의 마지막 세그먼트(일반적으로 *stdout*)에 추가하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="58d6a-848">`stdoutLogFile` 경로가 *stdout*으로 끝나는 경우 2018년 2월 5일 19시 42분 32초에 만들어진 PID 1934를 사용하는 앱에 대한 로그의 파일 이름은 *stdout_20180205194132_1934.log*입니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="58d6a-849">다음 샘플 `aspNetCore` 요소는 상대 경로 `.\log\`에서 stdout 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="58d6a-850">AppPool 사용자 ID에 제공된 경로에 쓸 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="58d6a-851">Azure App Service 배포를 위해 앱을 게시할 때는 웹 SDK가 `stdoutLogFile` 값을 `\\?\%home%\LogFiles\stdout`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="58d6a-852">`%home` 환경 변수는 Azure App Service에 의해 호스팅되는 앱에 대해 미리 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="58d6a-853">로깅 필터 규칙을 만들려면 ASP.NET Core 로깅 설명서의 [구성](xref:fundamentals/logging/index#log-filtering) 섹션과 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="58d6a-854">경로 형식에 대한 자세한 내용은 [Windows 시스템의 파일 경로 형식](/dotnet/standard/io/file-path-formats)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58d6a-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="58d6a-855">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="58d6a-856">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="58d6a-857">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="58d6a-858">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="58d6a-859">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="58d6a-860">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="58d6a-861">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="58d6a-862">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="58d6a-863">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="58d6a-864">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="58d6a-865">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="58d6a-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="58d6a-866">ASP.NET Core 모듈 설치 관리자는 **TrustedInstaller** 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="58d6a-867">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 *applicationHost.config*에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="58d6a-868">IIS 공유 구성을 사용할 경우 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="58d6a-869">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="58d6a-870">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-870">Run the installer.</span></span>
1. <span data-ttu-id="58d6a-871">업데이트된 *applicationHost.config* 파일을 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="58d6a-872">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="58d6a-873">모듈 버전 및 호스팅 번들 설치 관리자 로그</span><span class="sxs-lookup"><span data-stu-id="58d6a-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="58d6a-874">설치된 ASP.NET Core 모듈의 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="58d6a-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="58d6a-875">호스팅 시스템에서 *%windir%\System32\inetsrv*로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="58d6a-876">*aspnetcore.dll* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="58d6a-877">파일을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="58d6a-878">**세부 정보** 탭을 선택합니다. **파일 버전** 및 **제품 버전**은 설치된 모듈 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="58d6a-879">모듈에 대한 호스팅 번들 설치 관리자 로그는 *C:\\Users\\%UserName%\\AppData\\Local\\Temp*에 있습니다. 파일 이름은 *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="58d6a-880">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="58d6a-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="58d6a-881">Module</span><span class="sxs-lookup"><span data-stu-id="58d6a-881">Module</span></span>

<span data-ttu-id="58d6a-882">**IIS(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="58d6a-885">**IIS Express(x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="58d6a-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="58d6a-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="58d6a-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="58d6a-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="58d6a-888">스키마</span><span class="sxs-lookup"><span data-stu-id="58d6a-888">Schema</span></span>

<span data-ttu-id="58d6a-889">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-889">**IIS**</span></span>

* <span data-ttu-id="58d6a-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="58d6a-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-891">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="58d6a-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="58d6a-893">Configuration</span><span class="sxs-lookup"><span data-stu-id="58d6a-893">Configuration</span></span>

<span data-ttu-id="58d6a-894">**IIS**</span><span class="sxs-lookup"><span data-stu-id="58d6a-894">**IIS**</span></span>

* <span data-ttu-id="58d6a-895">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="58d6a-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="58d6a-896">**IIS Express**</span></span>

* <span data-ttu-id="58d6a-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="58d6a-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="58d6a-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="58d6a-899">*applicationHost.config* 파일에서 *aspnetcore*를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58d6a-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="58d6a-900">추가 자료</span><span class="sxs-lookup"><span data-stu-id="58d6a-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="58d6a-901">[ASP.NET Core 모듈 참조 소스(마스터 분기)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): **분기** 드롭다운 목록을 사용하여 특정 릴리스를 선택할 수 있습니다(예: `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="58d6a-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
