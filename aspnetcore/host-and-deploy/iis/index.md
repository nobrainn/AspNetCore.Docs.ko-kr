---
<span data-ttu-id="902bf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-102">'Blazor'</span></span>
- <span data-ttu-id="902bf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-103">'Identity'</span></span>
- <span data-ttu-id="902bf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-105">'Razor'</span></span>
- <span data-ttu-id="902bf-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="902bf-107">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="902bf-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="902bf-108">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="902bf-109">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="902bf-110">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="902bf-110">Supported operating systems</span></span>

<span data-ttu-id="902bf-111">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="902bf-112">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-112">Windows 7 or later</span></span>
* <span data-ttu-id="902bf-113">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="902bf-114">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="902bf-115">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="902bf-116">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="902bf-117">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="902bf-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="902bf-118">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="902bf-118">Supported platforms</span></span>

<span data-ttu-id="902bf-119">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="902bf-120">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="902bf-121">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="902bf-122">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="902bf-123">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="902bf-124">32비트(x86)용으로 게시된 앱은 해당 IIS 애플리케이션 풀에 대해 32비트를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="902bf-125">자세한 내용은 [IIS 사이트 만들기](#create-the-iis-site) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="902bf-126">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="902bf-127">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="902bf-128">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="902bf-129">In-process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-129">In-process hosting model</span></span>

<span data-ttu-id="902bf-130">In-process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="902bf-131">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-process 호스팅이 Out-of-process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="902bf-132">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="902bf-133">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="902bf-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="902bf-134">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-134">Performs app initialization.</span></span>
  * <span data-ttu-id="902bf-135">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="902bf-136">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="902bf-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="902bf-137">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="902bf-138">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 in-process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In-process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="902bf-140">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="902bf-141">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="902bf-142">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="902bf-143">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="902bf-144">IIS HTTP 서버에서 요청을 처리한 후:</span><span class="sxs-lookup"><span data-stu-id="902bf-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="902bf-145">해당 요청은 ASP.NET Core 미들웨어 파이프라인으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="902bf-146">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="902bf-147">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="902bf-148">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="902bf-149">In-process 호스팅은 기존 앱에 대해 옵트인(opt in)됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="902bf-150">ASP.NET Core 웹 템플릿은 In-process 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="902bf-151">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="902bf-152">성능 테스트의 결과 .NET Core 앱 In-process를 호스팅하는 것이 앱 Out-of-process 및 [Kestrel](xref:fundamentals/servers/kestrel)에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="902bf-153">단일 실행 파일로 게시된 앱은 In-process 호스팅 모델을 통해 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="902bf-154">Out-of-process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-154">Out-of-process hosting model</span></span>

<span data-ttu-id="902bf-155">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="902bf-156">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="902bf-157">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="902bf-158">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="902bf-160">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="902bf-161">드라이버는 웹 사이트의 구성된 포트에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="902bf-162">구성된 포트는 일반적으로 80(HTTP) 또는 443(HTTPS)입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="902bf-163">모듈은 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="902bf-164">임의 포트는 80 또는 443이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="902bf-165">ASP.NET Core 모듈은 시작 시 환경 변수를 통해 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="902bf-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="902bf-167">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="902bf-168">이 모듈은 HTTPS 전달을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="902bf-169">HTTPS를 통해 IIS에서 수신하는 경우에도 요청은 HTTP를 통해 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="902bf-170">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="902bf-171">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="902bf-172">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="902bf-173">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="902bf-174">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="902bf-175">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="902bf-176">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="902bf-177">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="902bf-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="902bf-178">`CreateHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="902bf-179">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="902bf-180">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-180">IIS options</span></span>

<span data-ttu-id="902bf-181">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="902bf-181">**In-process hosting model**</span></span>

<span data-ttu-id="902bf-182">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="902bf-183">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="902bf-184">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-184">Option</span></span>                         | <span data-ttu-id="902bf-185">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-185">Default</span></span> | <span data-ttu-id="902bf-186">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-186">Setting</span></span> |
| ---
<span data-ttu-id="902bf-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-188">'Blazor'</span></span>
- <span data-ttu-id="902bf-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-189">'Identity'</span></span>
- <span data-ttu-id="902bf-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-191">'Razor'</span></span>
- <span data-ttu-id="902bf-192">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-194">'Blazor'</span></span>
- <span data-ttu-id="902bf-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-195">'Identity'</span></span>
- <span data-ttu-id="902bf-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-197">'Razor'</span></span>
- <span data-ttu-id="902bf-198">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-200">'Blazor'</span></span>
- <span data-ttu-id="902bf-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-201">'Identity'</span></span>
- <span data-ttu-id="902bf-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-203">'Razor'</span></span>
- <span data-ttu-id="902bf-204">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-206">'Blazor'</span></span>
- <span data-ttu-id="902bf-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-207">'Identity'</span></span>
- <span data-ttu-id="902bf-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-209">'Razor'</span></span>
- <span data-ttu-id="902bf-210">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-212">'Blazor'</span></span>
- <span data-ttu-id="902bf-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-213">'Identity'</span></span>
- <span data-ttu-id="902bf-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-215">'Razor'</span></span>
- <span data-ttu-id="902bf-216">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-218">'Blazor'</span></span>
- <span data-ttu-id="902bf-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-219">'Identity'</span></span>
- <span data-ttu-id="902bf-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-221">'Razor'</span></span>
- <span data-ttu-id="902bf-222">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-224">'Blazor'</span></span>
- <span data-ttu-id="902bf-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-225">'Identity'</span></span>
- <span data-ttu-id="902bf-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-227">'Razor'</span></span>
- <span data-ttu-id="902bf-228">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-230">'Blazor'</span></span>
- <span data-ttu-id="902bf-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-231">'Identity'</span></span>
- <span data-ttu-id="902bf-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-233">'Razor'</span></span>
- <span data-ttu-id="902bf-234">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-236">'Blazor'</span></span>
- <span data-ttu-id="902bf-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-237">'Identity'</span></span>
- <span data-ttu-id="902bf-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-239">'Razor'</span></span>
- <span data-ttu-id="902bf-240">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-242">'Blazor'</span></span>
- <span data-ttu-id="902bf-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-243">'Identity'</span></span>
- <span data-ttu-id="902bf-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-245">'Razor'</span></span>
- <span data-ttu-id="902bf-246">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-248">'Blazor'</span></span>
- <span data-ttu-id="902bf-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-249">'Identity'</span></span>
- <span data-ttu-id="902bf-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-251">'Razor'</span></span>
- <span data-ttu-id="902bf-252">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-254">'Blazor'</span></span>
- <span data-ttu-id="902bf-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-255">'Identity'</span></span>
- <span data-ttu-id="902bf-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-257">'Razor'</span></span>
- <span data-ttu-id="902bf-258">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-260">'Blazor'</span></span>
- <span data-ttu-id="902bf-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-261">'Identity'</span></span>
- <span data-ttu-id="902bf-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-263">'Razor'</span></span>
- <span data-ttu-id="902bf-264">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-264">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-266">'Blazor'</span></span>
- <span data-ttu-id="902bf-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-267">'Identity'</span></span>
- <span data-ttu-id="902bf-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-269">'Razor'</span></span>
- <span data-ttu-id="902bf-270">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-270">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-271">---- | | `AutomaticAuthentication`      | `true`  | `true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-272">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-273">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-274">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-275">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="902bf-276">| | `AllowSynchronousIO`           | `false` | `HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="902bf-277">| | `MaxRequestBodySize`           | `30000000` | `HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="902bf-278">IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="902bf-279">`MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="902bf-280">`maxAllowedContentLength`를 늘리려면 *web.config*에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="902bf-281">자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="902bf-282">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="902bf-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="902bf-283">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="902bf-284">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="902bf-285">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-285">Option</span></span>                         | <span data-ttu-id="902bf-286">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-286">Default</span></span> | <span data-ttu-id="902bf-287">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-287">Setting</span></span> |
| ---
<span data-ttu-id="902bf-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-289">'Blazor'</span></span>
- <span data-ttu-id="902bf-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-290">'Identity'</span></span>
- <span data-ttu-id="902bf-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-292">'Razor'</span></span>
- <span data-ttu-id="902bf-293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-295">'Blazor'</span></span>
- <span data-ttu-id="902bf-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-296">'Identity'</span></span>
- <span data-ttu-id="902bf-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-298">'Razor'</span></span>
- <span data-ttu-id="902bf-299">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-301">'Blazor'</span></span>
- <span data-ttu-id="902bf-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-302">'Identity'</span></span>
- <span data-ttu-id="902bf-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-304">'Razor'</span></span>
- <span data-ttu-id="902bf-305">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-307">'Blazor'</span></span>
- <span data-ttu-id="902bf-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-308">'Identity'</span></span>
- <span data-ttu-id="902bf-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-310">'Razor'</span></span>
- <span data-ttu-id="902bf-311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-313">'Blazor'</span></span>
- <span data-ttu-id="902bf-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-314">'Identity'</span></span>
- <span data-ttu-id="902bf-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-316">'Razor'</span></span>
- <span data-ttu-id="902bf-317">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-319">'Blazor'</span></span>
- <span data-ttu-id="902bf-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-320">'Identity'</span></span>
- <span data-ttu-id="902bf-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-322">'Razor'</span></span>
- <span data-ttu-id="902bf-323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-325">'Blazor'</span></span>
- <span data-ttu-id="902bf-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-326">'Identity'</span></span>
- <span data-ttu-id="902bf-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-328">'Razor'</span></span>
- <span data-ttu-id="902bf-329">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-331">'Blazor'</span></span>
- <span data-ttu-id="902bf-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-332">'Identity'</span></span>
- <span data-ttu-id="902bf-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-334">'Razor'</span></span>
- <span data-ttu-id="902bf-335">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-337">'Blazor'</span></span>
- <span data-ttu-id="902bf-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-338">'Identity'</span></span>
- <span data-ttu-id="902bf-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-340">'Razor'</span></span>
- <span data-ttu-id="902bf-341">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-343">'Blazor'</span></span>
- <span data-ttu-id="902bf-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-344">'Identity'</span></span>
- <span data-ttu-id="902bf-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-346">'Razor'</span></span>
- <span data-ttu-id="902bf-347">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-349">'Blazor'</span></span>
- <span data-ttu-id="902bf-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-350">'Identity'</span></span>
- <span data-ttu-id="902bf-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-352">'Razor'</span></span>
- <span data-ttu-id="902bf-353">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-355">'Blazor'</span></span>
- <span data-ttu-id="902bf-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-356">'Identity'</span></span>
- <span data-ttu-id="902bf-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-358">'Razor'</span></span>
- <span data-ttu-id="902bf-359">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-361">'Blazor'</span></span>
- <span data-ttu-id="902bf-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-362">'Identity'</span></span>
- <span data-ttu-id="902bf-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-364">'Razor'</span></span>
- <span data-ttu-id="902bf-365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-365">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-367">'Blazor'</span></span>
- <span data-ttu-id="902bf-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-368">'Identity'</span></span>
- <span data-ttu-id="902bf-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-370">'Razor'</span></span>
- <span data-ttu-id="902bf-371">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-371">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-372">---- | | `AutomaticAuthentication`      | `true`  | `true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-373">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-374">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-375">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="902bf-376">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="902bf-377">| | `ForwardClientCertificate`     | `true`  | `true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="902bf-378">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="902bf-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="902bf-379">[IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 다음을 전달하도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="902bf-380">체계(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="902bf-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="902bf-381">요청이 시작된 원격 IP 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="902bf-382">[IIS 통합 미들웨어](#enable-the-iisintegration-components)는 전달된 헤더 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="902bf-383">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="902bf-384">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="902bf-385">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="902bf-385">web.config file</span></span>

<span data-ttu-id="902bf-386">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-387">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="902bf-388">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="902bf-389">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="902bf-390">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="902bf-391">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="902bf-392">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="902bf-393">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="902bf-394">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="902bf-395">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="902bf-396">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="902bf-397">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="902bf-398">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="902bf-398">web.config file location</span></span>

<span data-ttu-id="902bf-399">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="902bf-400">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="902bf-401">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="902bf-402">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="902bf-403">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="902bf-404">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="902bf-405">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="902bf-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="902bf-406">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="902bf-406">Transform web.config</span></span>

<span data-ttu-id="902bf-407">게시할 때 *web.config*를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="902bf-408">구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 *web.config*를 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="902bf-409">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-409">IIS configuration</span></span>

<span data-ttu-id="902bf-410">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="902bf-411">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="902bf-412">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="902bf-413">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="902bf-415">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="902bf-416">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="902bf-418">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-419">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="902bf-420">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-421">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-422">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-423">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-424">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="902bf-425">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-426">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-427">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="902bf-428">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="902bf-429">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="902bf-430">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-431">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="902bf-432">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="902bf-433">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="902bf-434">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="902bf-435">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-436">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="902bf-437">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-438">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="902bf-439">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-440">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-441">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-442">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-443">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="902bf-444">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-445">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-446">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-446">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="902bf-448">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="902bf-449">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="902bf-450">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-451">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="902bf-452">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="902bf-453">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="902bf-454">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="902bf-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="902bf-455">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="902bf-456">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="902bf-456">Direct download (current version)</span></span>

<span data-ttu-id="902bf-457">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="902bf-458">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="902bf-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="902bf-459">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="902bf-459">Earlier versions of the installer</span></span>

<span data-ttu-id="902bf-460">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="902bf-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="902bf-461">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="902bf-462">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="902bf-463">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="902bf-464">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="902bf-465">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="902bf-466">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="902bf-467">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="902bf-468">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-468">Run the installer on the server.</span></span> <span data-ttu-id="902bf-469">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="902bf-470">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="902bf-471">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="902bf-472">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-473">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="902bf-474">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-475">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="902bf-476">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="902bf-477">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="902bf-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="902bf-479">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="902bf-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="902bf-480">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="902bf-481">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="902bf-482">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="902bf-483">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="902bf-484">새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="902bf-485">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="902bf-486">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="902bf-487">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="902bf-488">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="902bf-489">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="902bf-490">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="902bf-491">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="902bf-491">Create the IIS site</span></span>

1. <span data-ttu-id="902bf-492">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="902bf-493">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="902bf-494">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="902bf-495">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="902bf-496">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="902bf-497">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-498">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="902bf-499">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="902bf-501">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="902bf-502">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="902bf-503">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="902bf-504">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="902bf-505">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="902bf-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="902bf-506">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="902bf-507">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="902bf-508">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-509">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="902bf-511">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="902bf-512">ASP.NET Core에서는 데스크톱 CLR(.NET CLR)을 로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="902bf-513">.NET Core에 대한 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="902bf-514">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="902bf-515">*ASP.NET Core 2.2 이상*:</span><span class="sxs-lookup"><span data-stu-id="902bf-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="902bf-516">[In-Process 호스팅 모델](#in-process-hosting-model)을 사용하는 32비트 SDK로 게시된 32비트(x86) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트의 애플리케이션 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="902bf-517">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="902bf-518">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-518">Select the app's Application Pool.</span></span> <span data-ttu-id="902bf-519">**작업** 사이드바에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="902bf-520">**32비트 애플리케이션 사용**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="902bf-521">[In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="902bf-522">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="902bf-523">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-523">Select the app's Application Pool.</span></span> <span data-ttu-id="902bf-524">**작업** 사이드바에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="902bf-525">**32비트 애플리케이션 사용**을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="902bf-526">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="902bf-527">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="902bf-528">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="902bf-529">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="902bf-530">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="902bf-531">앱 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-531">Deploy the app</span></span>

<span data-ttu-id="902bf-532">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="902bf-533">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="902bf-534">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="902bf-535">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="902bf-536">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="902bf-538">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="902bf-539">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="902bf-540">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="902bf-541">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="902bf-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="902bf-542">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="902bf-543">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="902bf-544">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="902bf-544">Browse the website</span></span>

<span data-ttu-id="902bf-545">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="902bf-546">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="902bf-547">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-547">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="902bf-549">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="902bf-549">Locked deployment files</span></span>

<span data-ttu-id="902bf-550">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="902bf-551">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="902bf-552">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-553">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="902bf-554">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="902bf-555">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="902bf-556">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="902bf-557">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="902bf-558">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="902bf-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="902bf-559">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="902bf-559">Data protection</span></span>

<span data-ttu-id="902bf-560">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="902bf-561">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="902bf-562">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="902bf-563">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="902bf-564">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="902bf-565">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="902bf-566">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="902bf-567">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="902bf-568">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-569">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="902bf-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="902bf-570">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="902bf-571">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="902bf-572">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="902bf-573">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="902bf-574">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="902bf-575">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="902bf-576">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="902bf-577">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="902bf-578">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="902bf-579">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="902bf-580">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="902bf-581">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="902bf-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="902bf-582">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="902bf-583">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="902bf-584">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="902bf-585">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="902bf-586">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="902bf-587">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="902bf-588">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="902bf-589">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="902bf-590">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="902bf-591">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="902bf-592">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="902bf-593">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="902bf-594">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="902bf-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="902bf-595">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="902bf-596">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="902bf-597">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="902bf-598">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="902bf-599">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="902bf-600">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="902bf-601">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="902bf-602">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="902bf-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="902bf-603">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="902bf-604">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="902bf-605">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="902bf-605">Virtual Directories</span></span>

<span data-ttu-id="902bf-606">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="902bf-607">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="902bf-608">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="902bf-608">Sub-applications</span></span>

<span data-ttu-id="902bf-609">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="902bf-610">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="902bf-611">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="902bf-612">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="902bf-613">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="902bf-614">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="902bf-615">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="902bf-616">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="902bf-617">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="902bf-618">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="902bf-619">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="902bf-620">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="902bf-621">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="902bf-622">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="902bf-623">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="902bf-624">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-624">Select **OK**.</span></span>

<span data-ttu-id="902bf-625">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="902bf-626">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="902bf-627">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="902bf-628">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="902bf-629">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="902bf-630">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="902bf-631">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="902bf-632">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="902bf-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="902bf-633">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="902bf-634">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="902bf-634">Configuration sections of web.config</span></span>

<span data-ttu-id="902bf-635">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="902bf-636">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="902bf-637">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="902bf-638">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="902bf-638">Application Pools</span></span>

<span data-ttu-id="902bf-639">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="902bf-640">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="902bf-641">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="902bf-642">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="902bf-643">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="902bf-644">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="902bf-645">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="902bf-645">Application Pool Identity</span></span>

<span data-ttu-id="902bf-646">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="902bf-647">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="902bf-648">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="902bf-650">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="902bf-651">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="902bf-652">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="902bf-653">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="902bf-654">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="902bf-655">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="902bf-656">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="902bf-657">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="902bf-658">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="902bf-659">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-659">Select the **Check Names** button.</span></span> <span data-ttu-id="902bf-660">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="902bf-661">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="902bf-662">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="902bf-663">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="902bf-665">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-665">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="902bf-667">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="902bf-668">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="902bf-669">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="902bf-670">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="902bf-671">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="902bf-672">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="902bf-672">HTTP/2 support</span></span>

<span data-ttu-id="902bf-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="902bf-674">In-process</span><span class="sxs-lookup"><span data-stu-id="902bf-674">In-process</span></span>
  * <span data-ttu-id="902bf-675">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="902bf-676">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="902bf-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="902bf-677">Out of Process</span><span class="sxs-lookup"><span data-stu-id="902bf-677">Out-of-process</span></span>
  * <span data-ttu-id="902bf-678">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="902bf-679">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="902bf-680">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="902bf-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="902bf-681">In-process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="902bf-682">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="902bf-683">In-process 및 Out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="902bf-684">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="902bf-685">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="902bf-686">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="902bf-687">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="902bf-687">CORS preflight requests</span></span>

<span data-ttu-id="902bf-688">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="902bf-689">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="902bf-690">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="902bf-691">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="902bf-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="902bf-692">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="902bf-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="902bf-693">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="902bf-694">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="902bf-695">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="902bf-695">Application Initialization Module</span></span>

<span data-ttu-id="902bf-696">‘앱 호스팅 In-process 및 Out-of-process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="902bf-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="902bf-697">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="902bf-698">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-698">The request triggers the app to start.</span></span> <span data-ttu-id="902bf-699">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="902bf-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="902bf-700">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="902bf-701">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="902bf-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="902bf-702">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="902bf-703">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="902bf-704">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="902bf-705">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="902bf-706">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="902bf-707">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="902bf-708">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="902bf-709">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="902bf-710">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="902bf-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="902bf-711">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="902bf-712">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="902bf-713">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="902bf-714">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="902bf-715">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-715">Select **OK**.</span></span>
  1. <span data-ttu-id="902bf-716">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="902bf-717">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="902bf-718">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="902bf-719">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="902bf-720">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-720">Select **OK**.</span></span>

* <span data-ttu-id="902bf-721">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="902bf-722">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="902bf-722">Idle Timeout</span></span>

<span data-ttu-id="902bf-723">‘앱 호스팅 In-process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="902bf-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="902bf-724">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="902bf-725">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="902bf-726">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="902bf-727">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="902bf-728">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="902bf-729">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-729">Select **OK**.</span></span>
1. <span data-ttu-id="902bf-730">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-730">Recycle the worker process.</span></span>

<span data-ttu-id="902bf-731">앱 호스팅 [Out-of-process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="902bf-732">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="902bf-733">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="902bf-734">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="902bf-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="902bf-735">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="902bf-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="902bf-736">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="902bf-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="902bf-737">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="902bf-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="902bf-738">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="902bf-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="902bf-739">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="902bf-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="902bf-740">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="902bf-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="902bf-741">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="902bf-742">추가 자료</span><span class="sxs-lookup"><span data-stu-id="902bf-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="902bf-743">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="902bf-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="902bf-744">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="902bf-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="902bf-745">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="902bf-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="902bf-746">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="902bf-747">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="902bf-748">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="902bf-748">Supported operating systems</span></span>

<span data-ttu-id="902bf-749">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="902bf-750">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-750">Windows 7 or later</span></span>
* <span data-ttu-id="902bf-751">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="902bf-752">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="902bf-753">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="902bf-754">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="902bf-755">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="902bf-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="902bf-756">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="902bf-756">Supported platforms</span></span>

<span data-ttu-id="902bf-757">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="902bf-758">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="902bf-759">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="902bf-760">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="902bf-761">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="902bf-762">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="902bf-763">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="902bf-764">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="902bf-765">In-process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-765">In-process hosting model</span></span>

<span data-ttu-id="902bf-766">In-process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="902bf-767">In-process 호스팅은 다음과 같은 이유로 Out-of-process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="902bf-768">요청은 루프백 어댑터를 통해 프록시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="902bf-769">루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환하는 네트워크 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="902bf-770">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="902bf-771">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="902bf-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="902bf-772">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-772">Performs app initialization.</span></span>
  * <span data-ttu-id="902bf-773">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="902bf-774">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="902bf-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="902bf-775">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="902bf-776">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="902bf-777">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In-process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="902bf-779">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="902bf-780">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="902bf-781">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="902bf-782">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="902bf-783">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="902bf-784">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="902bf-785">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="902bf-786">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="902bf-787">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="902bf-788">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="902bf-789">성능 테스트의 결과 .NET Core 앱을 In-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="902bf-790">Out-of-process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="902bf-790">Out-of-process hosting model</span></span>

<span data-ttu-id="902bf-791">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="902bf-792">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="902bf-793">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="902bf-794">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="902bf-796">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="902bf-797">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="902bf-798">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="902bf-799">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="902bf-800">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="902bf-801">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="902bf-802">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="902bf-803">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="902bf-804">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="902bf-805">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="902bf-806">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="902bf-807">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="902bf-808">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="902bf-809">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="902bf-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="902bf-810">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="902bf-811">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="902bf-812">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-812">IIS options</span></span>

<span data-ttu-id="902bf-813">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="902bf-813">**In-process hosting model**</span></span>

<span data-ttu-id="902bf-814">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="902bf-815">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="902bf-816">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-816">Option</span></span>                         | <span data-ttu-id="902bf-817">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-817">Default</span></span> | <span data-ttu-id="902bf-818">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-818">Setting</span></span> |
| ---
<span data-ttu-id="902bf-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-820">'Blazor'</span></span>
- <span data-ttu-id="902bf-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-821">'Identity'</span></span>
- <span data-ttu-id="902bf-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-823">'Razor'</span></span>
- <span data-ttu-id="902bf-824">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-826">'Blazor'</span></span>
- <span data-ttu-id="902bf-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-827">'Identity'</span></span>
- <span data-ttu-id="902bf-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-829">'Razor'</span></span>
- <span data-ttu-id="902bf-830">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-832">'Blazor'</span></span>
- <span data-ttu-id="902bf-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-833">'Identity'</span></span>
- <span data-ttu-id="902bf-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-835">'Razor'</span></span>
- <span data-ttu-id="902bf-836">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-838">'Blazor'</span></span>
- <span data-ttu-id="902bf-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-839">'Identity'</span></span>
- <span data-ttu-id="902bf-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-841">'Razor'</span></span>
- <span data-ttu-id="902bf-842">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-844">'Blazor'</span></span>
- <span data-ttu-id="902bf-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-845">'Identity'</span></span>
- <span data-ttu-id="902bf-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-847">'Razor'</span></span>
- <span data-ttu-id="902bf-848">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-850">'Blazor'</span></span>
- <span data-ttu-id="902bf-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-851">'Identity'</span></span>
- <span data-ttu-id="902bf-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-853">'Razor'</span></span>
- <span data-ttu-id="902bf-854">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-856">'Blazor'</span></span>
- <span data-ttu-id="902bf-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-857">'Identity'</span></span>
- <span data-ttu-id="902bf-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-859">'Razor'</span></span>
- <span data-ttu-id="902bf-860">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-862">'Blazor'</span></span>
- <span data-ttu-id="902bf-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-863">'Identity'</span></span>
- <span data-ttu-id="902bf-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-865">'Razor'</span></span>
- <span data-ttu-id="902bf-866">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-868">'Blazor'</span></span>
- <span data-ttu-id="902bf-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-869">'Identity'</span></span>
- <span data-ttu-id="902bf-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-871">'Razor'</span></span>
- <span data-ttu-id="902bf-872">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-874">'Blazor'</span></span>
- <span data-ttu-id="902bf-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-875">'Identity'</span></span>
- <span data-ttu-id="902bf-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-877">'Razor'</span></span>
- <span data-ttu-id="902bf-878">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-880">'Blazor'</span></span>
- <span data-ttu-id="902bf-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-881">'Identity'</span></span>
- <span data-ttu-id="902bf-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-883">'Razor'</span></span>
- <span data-ttu-id="902bf-884">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-886">'Blazor'</span></span>
- <span data-ttu-id="902bf-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-887">'Identity'</span></span>
- <span data-ttu-id="902bf-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-889">'Razor'</span></span>
- <span data-ttu-id="902bf-890">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-892">'Blazor'</span></span>
- <span data-ttu-id="902bf-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-893">'Identity'</span></span>
- <span data-ttu-id="902bf-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-895">'Razor'</span></span>
- <span data-ttu-id="902bf-896">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-896">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-898">'Blazor'</span></span>
- <span data-ttu-id="902bf-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-899">'Identity'</span></span>
- <span data-ttu-id="902bf-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-901">'Razor'</span></span>
- <span data-ttu-id="902bf-902">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-902">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-903">---- | | `AutomaticAuthentication`      | `true`  | `true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-904">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-905">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-906">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-907">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="902bf-908">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="902bf-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="902bf-909">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="902bf-910">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="902bf-911">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-911">Option</span></span>                         | <span data-ttu-id="902bf-912">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-912">Default</span></span> | <span data-ttu-id="902bf-913">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-913">Setting</span></span> |
| ---
<span data-ttu-id="902bf-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-915">'Blazor'</span></span>
- <span data-ttu-id="902bf-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-916">'Identity'</span></span>
- <span data-ttu-id="902bf-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-918">'Razor'</span></span>
- <span data-ttu-id="902bf-919">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-921">'Blazor'</span></span>
- <span data-ttu-id="902bf-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-922">'Identity'</span></span>
- <span data-ttu-id="902bf-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-924">'Razor'</span></span>
- <span data-ttu-id="902bf-925">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-927">'Blazor'</span></span>
- <span data-ttu-id="902bf-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-928">'Identity'</span></span>
- <span data-ttu-id="902bf-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-930">'Razor'</span></span>
- <span data-ttu-id="902bf-931">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-933">'Blazor'</span></span>
- <span data-ttu-id="902bf-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-934">'Identity'</span></span>
- <span data-ttu-id="902bf-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-936">'Razor'</span></span>
- <span data-ttu-id="902bf-937">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-939">'Blazor'</span></span>
- <span data-ttu-id="902bf-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-940">'Identity'</span></span>
- <span data-ttu-id="902bf-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-942">'Razor'</span></span>
- <span data-ttu-id="902bf-943">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-945">'Blazor'</span></span>
- <span data-ttu-id="902bf-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-946">'Identity'</span></span>
- <span data-ttu-id="902bf-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-948">'Razor'</span></span>
- <span data-ttu-id="902bf-949">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-951">'Blazor'</span></span>
- <span data-ttu-id="902bf-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-952">'Identity'</span></span>
- <span data-ttu-id="902bf-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-954">'Razor'</span></span>
- <span data-ttu-id="902bf-955">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-957">'Blazor'</span></span>
- <span data-ttu-id="902bf-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-958">'Identity'</span></span>
- <span data-ttu-id="902bf-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-960">'Razor'</span></span>
- <span data-ttu-id="902bf-961">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-963">'Blazor'</span></span>
- <span data-ttu-id="902bf-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-964">'Identity'</span></span>
- <span data-ttu-id="902bf-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-966">'Razor'</span></span>
- <span data-ttu-id="902bf-967">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-969">'Blazor'</span></span>
- <span data-ttu-id="902bf-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-970">'Identity'</span></span>
- <span data-ttu-id="902bf-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-972">'Razor'</span></span>
- <span data-ttu-id="902bf-973">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-975">'Blazor'</span></span>
- <span data-ttu-id="902bf-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-976">'Identity'</span></span>
- <span data-ttu-id="902bf-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-978">'Razor'</span></span>
- <span data-ttu-id="902bf-979">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-981">'Blazor'</span></span>
- <span data-ttu-id="902bf-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-982">'Identity'</span></span>
- <span data-ttu-id="902bf-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-984">'Razor'</span></span>
- <span data-ttu-id="902bf-985">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-987">'Blazor'</span></span>
- <span data-ttu-id="902bf-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-988">'Identity'</span></span>
- <span data-ttu-id="902bf-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-990">'Razor'</span></span>
- <span data-ttu-id="902bf-991">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-991">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-993">'Blazor'</span></span>
- <span data-ttu-id="902bf-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-994">'Identity'</span></span>
- <span data-ttu-id="902bf-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-996">'Razor'</span></span>
- <span data-ttu-id="902bf-997">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-997">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-998">---- | | `AutomaticAuthentication`      | `true`  | `true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-999">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-1000">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-1001">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="902bf-1002">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="902bf-1003">| | `ForwardClientCertificate`     | `true`  | `true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="902bf-1004">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="902bf-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="902bf-1005">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="902bf-1006">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="902bf-1007">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="902bf-1008">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="902bf-1008">web.config file</span></span>

<span data-ttu-id="902bf-1009">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-1010">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="902bf-1011">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="902bf-1012">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="902bf-1013">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="902bf-1014">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="902bf-1015">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="902bf-1016">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="902bf-1017">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="902bf-1018">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="902bf-1019">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="902bf-1020">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="902bf-1021">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="902bf-1021">web.config file location</span></span>

<span data-ttu-id="902bf-1022">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="902bf-1023">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="902bf-1024">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="902bf-1025">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="902bf-1026">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="902bf-1027">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="902bf-1028">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="902bf-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="902bf-1029">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="902bf-1029">Transform web.config</span></span>

<span data-ttu-id="902bf-1030">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="902bf-1031">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-1031">IIS configuration</span></span>

<span data-ttu-id="902bf-1032">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="902bf-1033">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="902bf-1034">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="902bf-1035">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="902bf-1037">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="902bf-1038">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="902bf-1040">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-1041">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="902bf-1042">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-1043">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-1044">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-1045">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-1046">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="902bf-1047">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-1048">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-1049">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="902bf-1050">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="902bf-1051">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="902bf-1052">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-1053">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="902bf-1054">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="902bf-1055">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="902bf-1056">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="902bf-1057">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-1058">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="902bf-1059">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-1060">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="902bf-1061">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-1062">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-1063">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-1064">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-1065">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="902bf-1066">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-1067">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-1068">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1068">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="902bf-1070">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="902bf-1071">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="902bf-1072">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-1073">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="902bf-1074">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="902bf-1075">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="902bf-1076">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="902bf-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="902bf-1077">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="902bf-1078">다운로드</span><span class="sxs-lookup"><span data-stu-id="902bf-1078">Download</span></span>

1. <span data-ttu-id="902bf-1079">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="902bf-1080">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="902bf-1081">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="902bf-1082">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="902bf-1083">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="902bf-1084">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="902bf-1085">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="902bf-1086">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1086">Run the installer on the server.</span></span> <span data-ttu-id="902bf-1087">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="902bf-1088">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="902bf-1089">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="902bf-1090">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-1091">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="902bf-1092">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-1093">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="902bf-1094">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="902bf-1095">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="902bf-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="902bf-1097">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="902bf-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="902bf-1098">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="902bf-1099">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="902bf-1100">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="902bf-1101">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="902bf-1102">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="902bf-1103">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="902bf-1104">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="902bf-1105">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="902bf-1106">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="902bf-1107">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="902bf-1108">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="902bf-1109">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="902bf-1110">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="902bf-1111">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="902bf-1112">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="902bf-1113">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="902bf-1113">Create the IIS site</span></span>

1. <span data-ttu-id="902bf-1114">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="902bf-1115">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="902bf-1116">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="902bf-1117">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="902bf-1118">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="902bf-1119">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-1120">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="902bf-1121">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="902bf-1123">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="902bf-1124">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="902bf-1125">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="902bf-1126">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="902bf-1127">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="902bf-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="902bf-1128">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="902bf-1129">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="902bf-1130">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-1131">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="902bf-1133">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="902bf-1134">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="902bf-1135">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="902bf-1136">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="902bf-1137">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="902bf-1138">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="902bf-1139">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="902bf-1140">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="902bf-1141">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="902bf-1142">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="902bf-1143">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="902bf-1144">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="902bf-1145">앱 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1145">Deploy the app</span></span>

<span data-ttu-id="902bf-1146">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="902bf-1147">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="902bf-1148">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="902bf-1149">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="902bf-1150">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="902bf-1152">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="902bf-1153">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="902bf-1154">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="902bf-1155">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="902bf-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="902bf-1156">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="902bf-1157">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="902bf-1158">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="902bf-1158">Browse the website</span></span>

<span data-ttu-id="902bf-1159">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="902bf-1160">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="902bf-1161">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1161">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="902bf-1163">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="902bf-1163">Locked deployment files</span></span>

<span data-ttu-id="902bf-1164">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="902bf-1165">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="902bf-1166">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-1167">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="902bf-1168">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="902bf-1169">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="902bf-1170">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="902bf-1171">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="902bf-1172">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="902bf-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="902bf-1173">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="902bf-1173">Data protection</span></span>

<span data-ttu-id="902bf-1174">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="902bf-1175">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="902bf-1176">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="902bf-1177">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="902bf-1178">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="902bf-1179">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="902bf-1180">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="902bf-1181">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="902bf-1182">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-1183">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="902bf-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="902bf-1184">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="902bf-1185">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="902bf-1186">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="902bf-1187">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="902bf-1188">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="902bf-1189">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="902bf-1190">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="902bf-1191">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="902bf-1192">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="902bf-1193">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="902bf-1194">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="902bf-1195">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="902bf-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="902bf-1196">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="902bf-1197">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="902bf-1198">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="902bf-1199">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="902bf-1200">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="902bf-1201">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="902bf-1202">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="902bf-1203">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="902bf-1204">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="902bf-1205">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="902bf-1206">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="902bf-1207">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="902bf-1208">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="902bf-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="902bf-1209">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="902bf-1210">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="902bf-1211">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="902bf-1212">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="902bf-1213">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="902bf-1214">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="902bf-1215">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="902bf-1216">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="902bf-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="902bf-1217">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="902bf-1218">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="902bf-1219">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="902bf-1219">Virtual Directories</span></span>

<span data-ttu-id="902bf-1220">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="902bf-1221">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="902bf-1222">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="902bf-1222">Sub-applications</span></span>

<span data-ttu-id="902bf-1223">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="902bf-1224">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="902bf-1225">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="902bf-1226">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="902bf-1227">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="902bf-1228">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="902bf-1229">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="902bf-1230">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="902bf-1231">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="902bf-1232">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="902bf-1233">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="902bf-1234">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="902bf-1235">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="902bf-1236">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="902bf-1237">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="902bf-1238">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1238">Select **OK**.</span></span>

<span data-ttu-id="902bf-1239">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="902bf-1240">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="902bf-1241">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="902bf-1242">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="902bf-1243">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="902bf-1244">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="902bf-1245">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="902bf-1246">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="902bf-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="902bf-1247">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="902bf-1248">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="902bf-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="902bf-1249">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="902bf-1250">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="902bf-1251">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="902bf-1252">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="902bf-1252">Application Pools</span></span>

<span data-ttu-id="902bf-1253">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="902bf-1254">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="902bf-1255">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="902bf-1256">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="902bf-1257">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="902bf-1258">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="902bf-1259">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="902bf-1259">Application Pool Identity</span></span>

<span data-ttu-id="902bf-1260">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="902bf-1261">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="902bf-1262">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="902bf-1264">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="902bf-1265">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="902bf-1266">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="902bf-1267">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="902bf-1268">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="902bf-1269">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="902bf-1270">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="902bf-1271">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="902bf-1272">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="902bf-1273">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="902bf-1274">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="902bf-1275">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="902bf-1276">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="902bf-1277">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="902bf-1279">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1279">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="902bf-1281">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="902bf-1282">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="902bf-1283">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="902bf-1284">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="902bf-1285">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="902bf-1286">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="902bf-1286">HTTP/2 support</span></span>

<span data-ttu-id="902bf-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="902bf-1288">In-process</span><span class="sxs-lookup"><span data-stu-id="902bf-1288">In-process</span></span>
  * <span data-ttu-id="902bf-1289">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="902bf-1290">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="902bf-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="902bf-1291">Out of Process</span><span class="sxs-lookup"><span data-stu-id="902bf-1291">Out-of-process</span></span>
  * <span data-ttu-id="902bf-1292">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="902bf-1293">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="902bf-1294">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="902bf-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="902bf-1295">In-process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="902bf-1296">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="902bf-1297">In-process 및 Out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="902bf-1298">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="902bf-1299">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="902bf-1300">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="902bf-1301">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="902bf-1301">CORS preflight requests</span></span>

<span data-ttu-id="902bf-1302">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="902bf-1303">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="902bf-1304">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="902bf-1305">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="902bf-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="902bf-1306">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="902bf-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="902bf-1307">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="902bf-1308">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="902bf-1309">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="902bf-1309">Application Initialization Module</span></span>

<span data-ttu-id="902bf-1310">‘앱 호스팅 In-process 및 Out-of-process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="902bf-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="902bf-1311">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="902bf-1312">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1312">The request triggers the app to start.</span></span> <span data-ttu-id="902bf-1313">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="902bf-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="902bf-1314">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="902bf-1315">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="902bf-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="902bf-1316">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="902bf-1317">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="902bf-1318">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="902bf-1319">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="902bf-1320">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="902bf-1321">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="902bf-1322">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="902bf-1323">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="902bf-1324">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="902bf-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="902bf-1325">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="902bf-1326">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="902bf-1327">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="902bf-1328">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="902bf-1329">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="902bf-1330">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="902bf-1331">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="902bf-1332">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="902bf-1333">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="902bf-1334">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1334">Select **OK**.</span></span>

* <span data-ttu-id="902bf-1335">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="902bf-1336">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="902bf-1336">Idle Timeout</span></span>

<span data-ttu-id="902bf-1337">‘앱 호스팅 In-process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="902bf-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="902bf-1338">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="902bf-1339">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="902bf-1340">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="902bf-1341">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="902bf-1342">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="902bf-1343">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1343">Select **OK**.</span></span>
1. <span data-ttu-id="902bf-1344">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1344">Recycle the worker process.</span></span>

<span data-ttu-id="902bf-1345">앱 호스팅 [Out-of-process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="902bf-1346">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="902bf-1347">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="902bf-1348">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="902bf-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="902bf-1349">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="902bf-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="902bf-1350">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="902bf-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="902bf-1351">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="902bf-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="902bf-1352">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="902bf-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="902bf-1353">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="902bf-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="902bf-1354">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="902bf-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="902bf-1355">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="902bf-1356">추가 자료</span><span class="sxs-lookup"><span data-stu-id="902bf-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="902bf-1357">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="902bf-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="902bf-1358">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="902bf-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="902bf-1359">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="902bf-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="902bf-1360">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="902bf-1361">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="902bf-1362">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="902bf-1362">Supported operating systems</span></span>

<span data-ttu-id="902bf-1363">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="902bf-1364">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1364">Windows 7 or later</span></span>
* <span data-ttu-id="902bf-1365">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="902bf-1366">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="902bf-1367">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="902bf-1368">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="902bf-1369">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="902bf-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="902bf-1370">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="902bf-1370">Supported platforms</span></span>

<span data-ttu-id="902bf-1371">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="902bf-1372">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="902bf-1373">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="902bf-1374">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="902bf-1375">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="902bf-1376">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="902bf-1377">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="902bf-1378">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="902bf-1379">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](xref:fundamentals/servers/index#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-process*) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="902bf-1380">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="902bf-1381">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="902bf-1382">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="902bf-1383">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="902bf-1385">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="902bf-1386">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="902bf-1387">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="902bf-1388">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="902bf-1389">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="902bf-1390">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="902bf-1391">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="902bf-1392">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="902bf-1393">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="902bf-1394">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="902bf-1395">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="902bf-1396">ASP.NET Core 모듈은 동적 포트를 생성하여 백 엔드 프로세스에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="902bf-1397">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="902bf-1398">`UseIISIntegration`은 localhost IP 주소(`127.0.0.1`)의 동적 포트에서 수신 대기하도록 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="902bf-1399">동적 포트가 1234인 경우 Kestrel은 `127.0.0.1:1234`에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="902bf-1400">이 구성은 다음에서 제공된 다른 URL 구성을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="902bf-1401">Kestrel의 수신 대기 API</span><span class="sxs-lookup"><span data-stu-id="902bf-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="902bf-1402">[구성](xref:fundamentals/configuration/index)(또는 [명령줄 --urls 옵션](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="902bf-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="902bf-1403">모듈을 사용하는 경우 `UseUrls` 호출 또는 Kestrel의 `Listen` API가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="902bf-1404">`UseUrls` 또는 `Listen`을 호출하는 경우 Kestrel은 IIS 없이 앱을 실행할 때만 지정된 포트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="902bf-1405">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="902bf-1406">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="902bf-1407">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="902bf-1408">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="902bf-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="902bf-1409">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="902bf-1410">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="902bf-1411">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-1411">IIS options</span></span>

| <span data-ttu-id="902bf-1412">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-1412">Option</span></span>                         | <span data-ttu-id="902bf-1413">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-1413">Default</span></span> | <span data-ttu-id="902bf-1414">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-1414">Setting</span></span> |
| ---
<span data-ttu-id="902bf-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1416">'Blazor'</span></span>
- <span data-ttu-id="902bf-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1417">'Identity'</span></span>
- <span data-ttu-id="902bf-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1419">'Razor'</span></span>
- <span data-ttu-id="902bf-1420">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1422">'Blazor'</span></span>
- <span data-ttu-id="902bf-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1423">'Identity'</span></span>
- <span data-ttu-id="902bf-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1425">'Razor'</span></span>
- <span data-ttu-id="902bf-1426">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1428">'Blazor'</span></span>
- <span data-ttu-id="902bf-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1429">'Identity'</span></span>
- <span data-ttu-id="902bf-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1431">'Razor'</span></span>
- <span data-ttu-id="902bf-1432">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1434">'Blazor'</span></span>
- <span data-ttu-id="902bf-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1435">'Identity'</span></span>
- <span data-ttu-id="902bf-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1437">'Razor'</span></span>
- <span data-ttu-id="902bf-1438">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1440">'Blazor'</span></span>
- <span data-ttu-id="902bf-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1441">'Identity'</span></span>
- <span data-ttu-id="902bf-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1443">'Razor'</span></span>
- <span data-ttu-id="902bf-1444">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1446">'Blazor'</span></span>
- <span data-ttu-id="902bf-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1447">'Identity'</span></span>
- <span data-ttu-id="902bf-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1449">'Razor'</span></span>
- <span data-ttu-id="902bf-1450">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1452">'Blazor'</span></span>
- <span data-ttu-id="902bf-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1453">'Identity'</span></span>
- <span data-ttu-id="902bf-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1455">'Razor'</span></span>
- <span data-ttu-id="902bf-1456">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1458">'Blazor'</span></span>
- <span data-ttu-id="902bf-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1459">'Identity'</span></span>
- <span data-ttu-id="902bf-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1461">'Razor'</span></span>
- <span data-ttu-id="902bf-1462">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1464">'Blazor'</span></span>
- <span data-ttu-id="902bf-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1465">'Identity'</span></span>
- <span data-ttu-id="902bf-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1467">'Razor'</span></span>
- <span data-ttu-id="902bf-1468">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1470">'Blazor'</span></span>
- <span data-ttu-id="902bf-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1471">'Identity'</span></span>
- <span data-ttu-id="902bf-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1473">'Razor'</span></span>
- <span data-ttu-id="902bf-1474">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1476">'Blazor'</span></span>
- <span data-ttu-id="902bf-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1477">'Identity'</span></span>
- <span data-ttu-id="902bf-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1479">'Razor'</span></span>
- <span data-ttu-id="902bf-1480">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1482">'Blazor'</span></span>
- <span data-ttu-id="902bf-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1483">'Identity'</span></span>
- <span data-ttu-id="902bf-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1485">'Razor'</span></span>
- <span data-ttu-id="902bf-1486">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1488">'Blazor'</span></span>
- <span data-ttu-id="902bf-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1489">'Identity'</span></span>
- <span data-ttu-id="902bf-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1491">'Razor'</span></span>
- <span data-ttu-id="902bf-1492">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1494">'Blazor'</span></span>
- <span data-ttu-id="902bf-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1495">'Identity'</span></span>
- <span data-ttu-id="902bf-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1497">'Razor'</span></span>
- <span data-ttu-id="902bf-1498">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-1499">---- | | `AutomaticAuthentication`      | `true`  | `true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-1500">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-1501">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-1502">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-1503">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="902bf-1504">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="902bf-1505">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="902bf-1506">옵션</span><span class="sxs-lookup"><span data-stu-id="902bf-1506">Option</span></span>                         | <span data-ttu-id="902bf-1507">기본</span><span class="sxs-lookup"><span data-stu-id="902bf-1507">Default</span></span> | <span data-ttu-id="902bf-1508">설정</span><span class="sxs-lookup"><span data-stu-id="902bf-1508">Setting</span></span> |
| ---
<span data-ttu-id="902bf-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1510">'Blazor'</span></span>
- <span data-ttu-id="902bf-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1511">'Identity'</span></span>
- <span data-ttu-id="902bf-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1513">'Razor'</span></span>
- <span data-ttu-id="902bf-1514">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1516">'Blazor'</span></span>
- <span data-ttu-id="902bf-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1517">'Identity'</span></span>
- <span data-ttu-id="902bf-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1519">'Razor'</span></span>
- <span data-ttu-id="902bf-1520">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1522">'Blazor'</span></span>
- <span data-ttu-id="902bf-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1523">'Identity'</span></span>
- <span data-ttu-id="902bf-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1525">'Razor'</span></span>
- <span data-ttu-id="902bf-1526">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1528">'Blazor'</span></span>
- <span data-ttu-id="902bf-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1529">'Identity'</span></span>
- <span data-ttu-id="902bf-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1531">'Razor'</span></span>
- <span data-ttu-id="902bf-1532">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1534">'Blazor'</span></span>
- <span data-ttu-id="902bf-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1535">'Identity'</span></span>
- <span data-ttu-id="902bf-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1537">'Razor'</span></span>
- <span data-ttu-id="902bf-1538">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1540">'Blazor'</span></span>
- <span data-ttu-id="902bf-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1541">'Identity'</span></span>
- <span data-ttu-id="902bf-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1543">'Razor'</span></span>
- <span data-ttu-id="902bf-1544">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1546">'Blazor'</span></span>
- <span data-ttu-id="902bf-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1547">'Identity'</span></span>
- <span data-ttu-id="902bf-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1549">'Razor'</span></span>
- <span data-ttu-id="902bf-1550">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1552">'Blazor'</span></span>
- <span data-ttu-id="902bf-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1553">'Identity'</span></span>
- <span data-ttu-id="902bf-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1555">'Razor'</span></span>
- <span data-ttu-id="902bf-1556">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1558">'Blazor'</span></span>
- <span data-ttu-id="902bf-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1559">'Identity'</span></span>
- <span data-ttu-id="902bf-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1561">'Razor'</span></span>
- <span data-ttu-id="902bf-1562">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1564">'Blazor'</span></span>
- <span data-ttu-id="902bf-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1565">'Identity'</span></span>
- <span data-ttu-id="902bf-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1567">'Razor'</span></span>
- <span data-ttu-id="902bf-1568">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1570">'Blazor'</span></span>
- <span data-ttu-id="902bf-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1571">'Identity'</span></span>
- <span data-ttu-id="902bf-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1573">'Razor'</span></span>
- <span data-ttu-id="902bf-1574">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1576">'Blazor'</span></span>
- <span data-ttu-id="902bf-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1577">'Identity'</span></span>
- <span data-ttu-id="902bf-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1579">'Razor'</span></span>
- <span data-ttu-id="902bf-1580">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="902bf-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1582">'Blazor'</span></span>
- <span data-ttu-id="902bf-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1583">'Identity'</span></span>
- <span data-ttu-id="902bf-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1585">'Razor'</span></span>
- <span data-ttu-id="902bf-1586">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="902bf-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="902bf-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1588">'Blazor'</span></span>
- <span data-ttu-id="902bf-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="902bf-1589">'Identity'</span></span>
- <span data-ttu-id="902bf-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="902bf-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="902bf-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="902bf-1591">'Razor'</span></span>
- <span data-ttu-id="902bf-1592">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="902bf-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="902bf-1593">---- | | `AutomaticAuthentication`      | `true`  | `true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="902bf-1594">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="902bf-1595">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="902bf-1596">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="902bf-1597">| | `AuthenticationDisplayName`    | `null` | 로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="902bf-1598">| | `ForwardClientCertificate`     | `true`  | `true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="902bf-1599">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="902bf-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="902bf-1600">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="902bf-1601">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="902bf-1602">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="902bf-1603">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="902bf-1603">web.config file</span></span>

<span data-ttu-id="902bf-1604">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-1605">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="902bf-1606">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="902bf-1607">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="902bf-1608">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="902bf-1609">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="902bf-1610">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="902bf-1611">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="902bf-1612">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="902bf-1613">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="902bf-1614">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="902bf-1615">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="902bf-1616">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="902bf-1616">web.config file location</span></span>

<span data-ttu-id="902bf-1617">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="902bf-1618">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="902bf-1619">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="902bf-1620">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="902bf-1621">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="902bf-1622">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="902bf-1623">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="902bf-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="902bf-1624">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="902bf-1624">Transform web.config</span></span>

<span data-ttu-id="902bf-1625">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="902bf-1626">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-1626">IIS configuration</span></span>

<span data-ttu-id="902bf-1627">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="902bf-1628">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="902bf-1629">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="902bf-1630">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="902bf-1632">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="902bf-1633">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="902bf-1635">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-1636">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="902bf-1637">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-1638">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-1639">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-1640">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-1641">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="902bf-1642">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-1643">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-1644">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="902bf-1645">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="902bf-1646">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="902bf-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="902bf-1647">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-1648">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="902bf-1649">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="902bf-1650">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="902bf-1651">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="902bf-1652">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="902bf-1653">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="902bf-1654">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="902bf-1655">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="902bf-1656">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="902bf-1657">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="902bf-1658">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="902bf-1659">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="902bf-1660">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="902bf-1661">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="902bf-1662">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="902bf-1663">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1663">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="902bf-1665">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="902bf-1666">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="902bf-1667">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="902bf-1668">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="902bf-1669">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="902bf-1670">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="902bf-1671">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="902bf-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="902bf-1672">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="902bf-1673">다운로드</span><span class="sxs-lookup"><span data-stu-id="902bf-1673">Download</span></span>

1. <span data-ttu-id="902bf-1674">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="902bf-1675">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="902bf-1676">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="902bf-1677">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="902bf-1678">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="902bf-1679">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="902bf-1680">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="902bf-1681">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1681">Run the installer on the server.</span></span> <span data-ttu-id="902bf-1682">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="902bf-1683">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="902bf-1684">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="902bf-1685">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-1686">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="902bf-1687">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="902bf-1688">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="902bf-1689">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="902bf-1690">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="902bf-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="902bf-1692">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="902bf-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="902bf-1693">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="902bf-1694">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="902bf-1695">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="902bf-1696">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="902bf-1697">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="902bf-1698">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="902bf-1699">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="902bf-1700">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="902bf-1701">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="902bf-1702">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="902bf-1703">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="902bf-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="902bf-1704">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="902bf-1705">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="902bf-1706">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="902bf-1707">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="902bf-1708">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="902bf-1708">Create the IIS site</span></span>

1. <span data-ttu-id="902bf-1709">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="902bf-1710">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="902bf-1711">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="902bf-1712">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="902bf-1713">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="902bf-1714">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-1715">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="902bf-1716">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="902bf-1718">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="902bf-1719">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="902bf-1720">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="902bf-1721">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="902bf-1722">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="902bf-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="902bf-1723">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="902bf-1724">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="902bf-1725">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="902bf-1726">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="902bf-1728">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="902bf-1729">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="902bf-1730">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="902bf-1731">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="902bf-1732">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="902bf-1733">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="902bf-1734">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="902bf-1735">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="902bf-1736">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="902bf-1737">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="902bf-1738">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="902bf-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="902bf-1739">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="902bf-1740">앱 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1740">Deploy the app</span></span>

<span data-ttu-id="902bf-1741">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="902bf-1742">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="902bf-1743">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="902bf-1744">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="902bf-1745">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="902bf-1747">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="902bf-1748">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="902bf-1749">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="902bf-1750">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="902bf-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="902bf-1751">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="902bf-1752">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="902bf-1753">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="902bf-1753">Browse the website</span></span>

<span data-ttu-id="902bf-1754">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="902bf-1755">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="902bf-1756">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1756">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="902bf-1758">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="902bf-1758">Locked deployment files</span></span>

<span data-ttu-id="902bf-1759">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="902bf-1760">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="902bf-1761">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-1762">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="902bf-1763">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="902bf-1764">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="902bf-1765">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="902bf-1766">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="902bf-1767">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="902bf-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="902bf-1768">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="902bf-1768">Data protection</span></span>

<span data-ttu-id="902bf-1769">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="902bf-1770">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="902bf-1771">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="902bf-1772">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="902bf-1773">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="902bf-1774">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="902bf-1775">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="902bf-1776">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="902bf-1777">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="902bf-1778">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="902bf-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="902bf-1779">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="902bf-1780">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="902bf-1781">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="902bf-1782">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="902bf-1783">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="902bf-1784">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="902bf-1785">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="902bf-1786">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="902bf-1787">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="902bf-1788">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="902bf-1789">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="902bf-1790">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="902bf-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="902bf-1791">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="902bf-1792">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="902bf-1793">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="902bf-1794">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="902bf-1795">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="902bf-1796">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="902bf-1797">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="902bf-1798">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="902bf-1799">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="902bf-1800">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="902bf-1801">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="902bf-1802">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="902bf-1803">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="902bf-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="902bf-1804">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="902bf-1805">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="902bf-1806">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="902bf-1807">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="902bf-1808">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="902bf-1809">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="902bf-1810">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="902bf-1811">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="902bf-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="902bf-1812">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="902bf-1813">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="902bf-1814">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="902bf-1814">Virtual Directories</span></span>

<span data-ttu-id="902bf-1815">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="902bf-1816">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="902bf-1817">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="902bf-1817">Sub-applications</span></span>

<span data-ttu-id="902bf-1818">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="902bf-1819">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="902bf-1820">하위 앱에는 ASP.NET Core 모듈이 처리기로 포함되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="902bf-1821">하위 앱의 *web.config* 파일에 모듈이 처리기로 추가되면, 하위 앱을 찾으려고 할 때 잘못된 구성 파일을 참조하는 *500.19 내부 서버 오류*가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="902bf-1822">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="902bf-1823">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하는 경우, 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="902bf-1824">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="902bf-1825">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="902bf-1826">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="902bf-1827">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="902bf-1828">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="902bf-1829">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="902bf-1830">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="902bf-1831">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="902bf-1832">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="902bf-1833">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="902bf-1834">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="902bf-1835">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="902bf-1836">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="902bf-1837">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1837">Select **OK**.</span></span>

<span data-ttu-id="902bf-1838">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="902bf-1839">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="902bf-1840">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="902bf-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="902bf-1841">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="902bf-1842">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="902bf-1843">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="902bf-1844">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="902bf-1845">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="902bf-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="902bf-1846">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="902bf-1847">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="902bf-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="902bf-1848">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="902bf-1849">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="902bf-1850">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="902bf-1851">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="902bf-1851">Application Pools</span></span>

<span data-ttu-id="902bf-1852">서버에서 여러 웹 사이트를 호스트하는 경우 각 앱을 해당 앱 풀에서 실행하여 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="902bf-1853">이 구성은 IIS **웹 사이트 추가** 대화 상자의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="902bf-1854">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="902bf-1855">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="902bf-1856">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="902bf-1856">Application Pool Identity</span></span>

<span data-ttu-id="902bf-1857">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="902bf-1858">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="902bf-1859">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="902bf-1861">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="902bf-1862">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="902bf-1863">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="902bf-1864">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="902bf-1865">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="902bf-1866">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="902bf-1867">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="902bf-1868">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="902bf-1869">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="902bf-1870">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="902bf-1871">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="902bf-1872">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="902bf-1873">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="902bf-1874">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="902bf-1876">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1876">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="902bf-1878">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="902bf-1879">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="902bf-1880">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="902bf-1881">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="902bf-1882">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="902bf-1883">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="902bf-1883">HTTP/2 support</span></span>

<span data-ttu-id="902bf-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음 기본 요구 사항을 충족하는 Out-of-process 배포에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="902bf-1885">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="902bf-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="902bf-1886">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="902bf-1887">대상 프레임워크: HTTP/2 연결은 IIS에 의해 완전히 처리되므로 Out-of-process 배포에는 해당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="902bf-1888">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="902bf-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="902bf-1889">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="902bf-1890">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="902bf-1891">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="902bf-1892">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="902bf-1893">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="902bf-1893">CORS preflight requests</span></span>

<span data-ttu-id="902bf-1894">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="902bf-1895">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="902bf-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="902bf-1896">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="902bf-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="902bf-1897">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="902bf-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="902bf-1898">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="902bf-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="902bf-1899">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="902bf-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="902bf-1900">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="902bf-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="902bf-1901">추가 자료</span><span class="sxs-lookup"><span data-stu-id="902bf-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="902bf-1902">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="902bf-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="902bf-1903">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="902bf-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="902bf-1904">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="902bf-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
