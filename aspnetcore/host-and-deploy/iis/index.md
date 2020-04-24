---
title: IIS가 있는 Windows에서 ASP.NET Core 호스팅
author: rick-anderson
description: Windows Server IIS(인터넷 정보 서비스)에서 ASP.NET Core 앱을 호스팅하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 77f07ba89de4449c6d13006a5fd61499cb5cdfc0
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642741"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="40744-103">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="40744-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40744-104">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="40744-105">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="40744-106">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="40744-106">Supported operating systems</span></span>

<span data-ttu-id="40744-107">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="40744-108">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="40744-108">Windows 7 or later</span></span>
* <span data-ttu-id="40744-109">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="40744-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="40744-110">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="40744-111">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="40744-112">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="40744-113">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="40744-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="40744-114">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="40744-114">Supported platforms</span></span>

<span data-ttu-id="40744-115">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="40744-116">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="40744-117">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="40744-118">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="40744-119">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="40744-120">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-120">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="40744-121">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-121">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="40744-122">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-122">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="40744-123">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-123">In-process hosting model</span></span>

<span data-ttu-id="40744-124">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-124">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="40744-125">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-125">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="40744-126">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-126">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="40744-127">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="40744-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="40744-128">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-128">Performs app initialization.</span></span>
  * <span data-ttu-id="40744-129">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-129">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="40744-130">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="40744-130">Calls `Program.Main`.</span></span>
* <span data-ttu-id="40744-131">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-131">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="40744-132">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-132">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="40744-133">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-133">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="40744-135">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-135">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="40744-136">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-136">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="40744-137">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-137">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="40744-138">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-138">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="40744-139">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-139">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="40744-140">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-140">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="40744-141">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-141">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="40744-142">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-142">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="40744-143">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-143">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="40744-144">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-144">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="40744-145">성능 테스트의 결과 .NET Core 앱을 in-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-145">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

> [!NOTE]
> <span data-ttu-id="40744-146">단일 실행 파일로 게시된 앱은 In Process 호스팅 모델을 통해 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-146">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="40744-147">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-147">Out-of-process hosting model</span></span>

<span data-ttu-id="40744-148">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-148">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="40744-149">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-149">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="40744-150">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-150">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="40744-151">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-151">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="40744-153">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-153">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="40744-154">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-154">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="40744-155">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-155">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="40744-156">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-156">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="40744-157">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-157">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="40744-158">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-158">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="40744-159">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-159">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="40744-160">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-160">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="40744-161">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-161">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="40744-162">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-162">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="40744-163">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-163">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="40744-164">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-164">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="40744-165">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="40744-165">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="40744-166">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="40744-166">Enable the IISIntegration components</span></span>

<span data-ttu-id="40744-167">`CreateHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-167">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="40744-168">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-168">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="40744-169">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="40744-169">IIS options</span></span>

<span data-ttu-id="40744-170">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="40744-170">**In-process hosting model**</span></span>

<span data-ttu-id="40744-171">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-171">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="40744-172">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-172">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="40744-173">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-173">Option</span></span>                         | <span data-ttu-id="40744-174">기본</span><span class="sxs-lookup"><span data-stu-id="40744-174">Default</span></span> | <span data-ttu-id="40744-175">설정</span><span class="sxs-lookup"><span data-stu-id="40744-175">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-176">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-176">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-177">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-177">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-178">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-178">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-179">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-179">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-180">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-180">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="40744-181">`HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-181">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="40744-182">`HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-182">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="40744-183">IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-183">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="40744-184">`MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-184">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="40744-185">`maxAllowedContentLength`를 늘리려면 *web.config*에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-185">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="40744-186">자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-186">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="40744-187">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="40744-187">**Out-of-process hosting model**</span></span>

<span data-ttu-id="40744-188">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-188">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="40744-189">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-189">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="40744-190">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-190">Option</span></span>                         | <span data-ttu-id="40744-191">기본</span><span class="sxs-lookup"><span data-stu-id="40744-191">Default</span></span> | <span data-ttu-id="40744-192">설정</span><span class="sxs-lookup"><span data-stu-id="40744-192">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-193">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-193">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-194">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-194">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-195">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-195">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-196">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-196">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-197">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-197">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="40744-198">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="40744-198">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="40744-199">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="40744-199">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="40744-200">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-200">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="40744-201">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-201">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="40744-202">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-202">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="40744-203">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="40744-203">web.config file</span></span>

<span data-ttu-id="40744-204">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-204">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-205">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-205">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="40744-206">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="40744-206">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="40744-207">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-207">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="40744-208">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-208">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="40744-209">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-209">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="40744-210">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-210">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="40744-211">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-211">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="40744-212">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-212">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="40744-213">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-213">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="40744-214">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-214">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="40744-215">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-215">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="40744-216">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="40744-216">web.config file location</span></span>

<span data-ttu-id="40744-217">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-217">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="40744-218">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-218">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="40744-219">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-219">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="40744-220">중요한 파일은 *\<assembly>.runtimeconfig.json*, *\<assembly>.xml*(XML 문서 주석) 및 *\<assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-220">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="40744-221">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-221">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="40744-222">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-222">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="40744-223">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="40744-223">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="40744-224">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="40744-224">Transform web.config</span></span>

<span data-ttu-id="40744-225">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-225">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="40744-226">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-226">IIS configuration</span></span>

<span data-ttu-id="40744-227">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-227">**Windows Server operating systems**</span></span>

<span data-ttu-id="40744-228">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-228">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="40744-229">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-229">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="40744-230">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-230">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="40744-232">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-232">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="40744-233">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-233">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="40744-235">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-235">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-236">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-236">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="40744-237">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-237">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-238">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-238">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-239">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-239">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-240">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-240">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-241">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-241">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="40744-242">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-242">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-243">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-243">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-244">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-244">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="40744-245">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-245">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="40744-246">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-246">**Windows desktop operating systems**</span></span>

<span data-ttu-id="40744-247">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-247">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-248">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-248">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="40744-249">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-249">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="40744-250">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-250">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="40744-251">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-251">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="40744-252">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-252">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-253">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-253">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="40744-254">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-254">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-255">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-255">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="40744-256">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-256">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-257">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-257">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-258">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-258">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-259">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-259">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-260">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-260">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="40744-261">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-261">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-262">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-262">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-263">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-263">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="40744-265">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-265">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="40744-266">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-266">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="40744-267">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-267">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-268">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-268">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40744-269">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-269">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="40744-270">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-270">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="40744-271">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="40744-271">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="40744-272">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-272">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="40744-273">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="40744-273">Direct download (current version)</span></span>

<span data-ttu-id="40744-274">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-274">Download the installer using the following link:</span></span>

[<span data-ttu-id="40744-275">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="40744-275">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="40744-276">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="40744-276">Earlier versions of the installer</span></span>

<span data-ttu-id="40744-277">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="40744-277">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="40744-278">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-278">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="40744-279">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-279">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="40744-280">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-280">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="40744-281">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-281">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="40744-282">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-282">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="40744-283">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-283">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="40744-284">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-284">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="40744-285">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-285">Run the installer on the server.</span></span> <span data-ttu-id="40744-286">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-286">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="40744-287">`OPT_NO_ANCM=1` &ndash; ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-287">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="40744-288">`OPT_NO_RUNTIME=1` &ndash; .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-288">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="40744-289">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-289">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-290">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-290">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="40744-291">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-291">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-292">`OPT_NO_X86=1` &ndash; x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-292">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="40744-293">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-293">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="40744-294">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-294">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="40744-295">`OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-295">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="40744-296">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="40744-296">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="40744-297">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-297">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="40744-298">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-298">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="40744-299">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-299">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="40744-300">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-300">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="40744-301">새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-301">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="40744-302">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-302">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="40744-303">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="40744-303">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="40744-304">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-304">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="40744-305">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-305">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="40744-306">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-306">The preferred method is to use WebPI.</span></span> <span data-ttu-id="40744-307">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-307">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="40744-308">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="40744-308">Create the IIS site</span></span>

1. <span data-ttu-id="40744-309">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-309">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="40744-310">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-310">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="40744-311">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-311">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="40744-312">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-312">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="40744-313">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-313">Right-click the **Sites** folder.</span></span> <span data-ttu-id="40744-314">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-314">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-315">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-315">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="40744-316">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-316">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="40744-318">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-318">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="40744-319">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-319">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="40744-320">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-320">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="40744-321">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-321">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="40744-322">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="40744-322">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="40744-323">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-323">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="40744-324">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-324">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="40744-325">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-325">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-326">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-326">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="40744-328">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-328">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="40744-329">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-329">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="40744-330">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-330">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="40744-331">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-331">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="40744-332">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-332">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="40744-333">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-333">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="40744-334">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-334">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="40744-335">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-335">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="40744-336">애플리케이션 풀의 기본 ID(**프로세스 모델** > **ID**)가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-336">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="40744-337">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-337">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="40744-338">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-338">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="40744-339">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-339">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="40744-340">앱 배포</span><span class="sxs-lookup"><span data-stu-id="40744-340">Deploy the app</span></span>

<span data-ttu-id="40744-341">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-341">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="40744-342">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-342">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="40744-343">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-343">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="40744-344">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-344">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="40744-345">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-345">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="40744-347">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-347">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="40744-348">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-348">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="40744-349">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-349">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="40744-350">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="40744-350">Alternatives to Web Deploy</span></span>

<span data-ttu-id="40744-351">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-351">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="40744-352">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-352">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="40744-353">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="40744-353">Browse the website</span></span>

<span data-ttu-id="40744-354">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-354">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="40744-355">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-355">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="40744-356">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-356">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="40744-358">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="40744-358">Locked deployment files</span></span>

<span data-ttu-id="40744-359">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-359">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="40744-360">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-360">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="40744-361">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-361">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-362">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-362">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="40744-363">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-363">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="40744-364">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-364">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="40744-365">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-365">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="40744-366">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-366">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="40744-367">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="40744-367">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="40744-368">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="40744-368">Data protection</span></span>

<span data-ttu-id="40744-369">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-369">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="40744-370">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-370">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="40744-371">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-371">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="40744-372">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-372">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="40744-373">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-373">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="40744-374">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-374">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="40744-375">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-375">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="40744-376">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-376">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="40744-377">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-377">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-378">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="40744-378">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="40744-379">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-379">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="40744-380">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-380">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="40744-381">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-381">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="40744-382">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-382">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="40744-383">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-383">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="40744-384">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-384">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="40744-385">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-385">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="40744-386">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-386">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="40744-387">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-387">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="40744-388">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-388">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="40744-389">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-389">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="40744-390">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="40744-390">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="40744-391">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-391">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="40744-392">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-392">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="40744-393">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-393">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="40744-394">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-394">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="40744-395">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-395">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="40744-396">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-396">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="40744-397">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-397">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="40744-398">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-398">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="40744-399">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-399">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="40744-400">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-400">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="40744-401">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-401">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="40744-402">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-402">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="40744-403">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="40744-403">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="40744-404">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-404">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="40744-405">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-405">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="40744-406">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-406">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="40744-407">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-407">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="40744-408">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-408">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="40744-409">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-409">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="40744-410">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-410">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="40744-411">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="40744-411">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="40744-412">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-412">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="40744-413">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-413">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="40744-414">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="40744-414">Virtual Directories</span></span>

<span data-ttu-id="40744-415">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-415">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="40744-416">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-416">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="40744-417">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="40744-417">Sub-applications</span></span>

<span data-ttu-id="40744-418">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-418">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="40744-419">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-419">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="40744-420">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-420">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="40744-421">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-421">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="40744-422">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-422">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="40744-423">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-423">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="40744-424">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-424">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="40744-425">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-425">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="40744-426">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다. </span><span class="sxs-lookup"><span data-stu-id="40744-426">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="40744-427">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-427">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="40744-428">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-428">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="40744-429">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-429">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="40744-430">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-430">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="40744-431">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-431">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="40744-432">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-432">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="40744-433">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-433">Select **OK**.</span></span>

<span data-ttu-id="40744-434">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-434">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="40744-435">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-435">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="40744-436">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-436">Configuration of IIS with web.config</span></span>

<span data-ttu-id="40744-437">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-437">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="40744-438">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-438">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="40744-439">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-439">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="40744-440">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-440">For more information, see the following topics:</span></span>

* [<span data-ttu-id="40744-441">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="40744-441">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="40744-442">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-442">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="40744-443">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="40744-443">Configuration sections of web.config</span></span>

<span data-ttu-id="40744-444">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-444">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="40744-445">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-445">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="40744-446">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-446">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="40744-447">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="40744-447">Application Pools</span></span>

<span data-ttu-id="40744-448">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-448">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="40744-449">In-process 호스팅 &ndash; 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-449">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="40744-450">Out-of-process 호스팅 &ndash; 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-450">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="40744-451">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-451">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="40744-452">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-452">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="40744-453">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-453">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="40744-454">애플리케이션 풀 ID</span><span class="sxs-lookup"><span data-stu-id="40744-454">Application Pool Identity</span></span>

<span data-ttu-id="40744-455">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-455">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="40744-456">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-456">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="40744-457">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **ID**가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-457">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="40744-459">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-459">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="40744-460">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-460">Resources can be secured using this identity.</span></span> <span data-ttu-id="40744-461">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-461">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="40744-462">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-462">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="40744-463">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-463">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="40744-464">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-464">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="40744-465">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-465">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="40744-466">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-466">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="40744-467">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-467">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="40744-468">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-468">Select the **Check Names** button.</span></span> <span data-ttu-id="40744-469">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-469">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="40744-470">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-470">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="40744-471">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-471">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="40744-472">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-472">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="40744-474">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-474">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="40744-476">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-476">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="40744-477">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-477">Provide additional permissions as needed.</span></span>

<span data-ttu-id="40744-478">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-478">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="40744-479">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-479">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="40744-480">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-480">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="40744-481">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="40744-481">HTTP/2 support</span></span>

<span data-ttu-id="40744-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-482">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="40744-483">In-Process</span><span class="sxs-lookup"><span data-stu-id="40744-483">In-process</span></span>
  * <span data-ttu-id="40744-484">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="40744-484">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="40744-485">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="40744-485">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="40744-486">Out of Process</span><span class="sxs-lookup"><span data-stu-id="40744-486">Out-of-process</span></span>
  * <span data-ttu-id="40744-487">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="40744-487">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="40744-488">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-488">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="40744-489">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="40744-489">TLS 1.2 or later connection</span></span>

<span data-ttu-id="40744-490">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-490">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="40744-491">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-491">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="40744-492">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-492">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="40744-493">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-493">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="40744-494">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-494">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="40744-495">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-495">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="40744-496">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="40744-496">CORS preflight requests</span></span>

<span data-ttu-id="40744-497">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="40744-497">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="40744-498">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-498">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="40744-499">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-499">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="40744-500">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="40744-500">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="40744-501">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="40744-501">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="40744-502">[애플리케이션 초기화 모듈](#application-initialization-module)&ndash; 앱 호스팅 [In Process](#in-process-hosting-model) 또는 [Out of Process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-502">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="40744-503">[유휴 시간 제한](#idle-timeout)&ndash; - 앱 호스팅 [In Process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-503">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="40744-504">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="40744-504">Application Initialization Module</span></span>

<span data-ttu-id="40744-505">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’ </span><span class="sxs-lookup"><span data-stu-id="40744-505">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="40744-506">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-506">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="40744-507">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-507">The request triggers the app to start.</span></span> <span data-ttu-id="40744-508">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="40744-508">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="40744-509">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-509">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="40744-510">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="40744-510">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="40744-511">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-511">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="40744-512">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-512">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="40744-513">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-513">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="40744-514">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="40744-514">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="40744-515">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-515">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="40744-516">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-516">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="40744-517">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-517">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="40744-518">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-518">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="40744-519">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="40744-519">Using IIS Manager:</span></span>

  1. <span data-ttu-id="40744-520">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-520">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="40744-521">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-521">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="40744-522">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-522">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="40744-523">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-523">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="40744-524">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-524">Select **OK**.</span></span>
  1. <span data-ttu-id="40744-525">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-525">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="40744-526">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-526">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="40744-527">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-527">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="40744-528">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-528">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="40744-529">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-529">Select **OK**.</span></span>

* <span data-ttu-id="40744-530">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-530">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="40744-531">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="40744-531">Idle Timeout</span></span>

<span data-ttu-id="40744-532">‘앱 호스팅 In Process 에만 적용됩니다.’ </span><span class="sxs-lookup"><span data-stu-id="40744-532">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="40744-533">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-533">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="40744-534">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-534">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="40744-535">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-535">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="40744-536">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-536">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="40744-537">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-537">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="40744-538">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-538">Select **OK**.</span></span>
1. <span data-ttu-id="40744-539">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-539">Recycle the worker process.</span></span>

<span data-ttu-id="40744-540">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-540">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="40744-541">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-541">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="40744-542">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-542">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="40744-543">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="40744-543">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="40744-544">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="40744-544">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="40744-545">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="40744-545">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="40744-546">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="40744-546">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="40744-547">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="40744-547">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="40744-548">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="40744-548">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="40744-549">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="40744-549">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="40744-550">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="40744-550">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="40744-551">추가 자료</span><span class="sxs-lookup"><span data-stu-id="40744-551">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="40744-552">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="40744-552">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="40744-553">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="40744-553">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="40744-554">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="40744-554">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="40744-555">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-555">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="40744-556">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-556">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="40744-557">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="40744-557">Supported operating systems</span></span>

<span data-ttu-id="40744-558">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-558">The following operating systems are supported:</span></span>

* <span data-ttu-id="40744-559">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="40744-559">Windows 7 or later</span></span>
* <span data-ttu-id="40744-560">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="40744-560">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="40744-561">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-561">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="40744-562">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-562">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="40744-563">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-563">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="40744-564">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="40744-564">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="40744-565">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="40744-565">Supported platforms</span></span>

<span data-ttu-id="40744-566">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-566">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="40744-567">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-567">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="40744-568">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-568">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="40744-569">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-569">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="40744-570">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-570">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="40744-571">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-571">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="40744-572">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-572">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="40744-573">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-573">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="40744-574">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-574">In-process hosting model</span></span>

<span data-ttu-id="40744-575">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-575">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="40744-576">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-576">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="40744-577">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-577">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="40744-578">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="40744-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="40744-579">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-579">Performs app initialization.</span></span>
  * <span data-ttu-id="40744-580">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-580">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="40744-581">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="40744-581">Calls `Program.Main`.</span></span>
* <span data-ttu-id="40744-582">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-582">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="40744-583">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-583">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="40744-584">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-584">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="40744-586">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-586">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="40744-587">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-587">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="40744-588">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-588">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="40744-589">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-589">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="40744-590">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-590">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="40744-591">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-591">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="40744-592">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-592">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="40744-593">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-593">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="40744-594">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-594">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="40744-595">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-595">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="40744-596">성능 테스트의 결과 .NET Core 앱을 in-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-596">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="40744-597">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="40744-597">Out-of-process hosting model</span></span>

<span data-ttu-id="40744-598">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-598">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="40744-599">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-599">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="40744-600">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-600">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="40744-601">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-601">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="40744-603">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-603">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="40744-604">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-604">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="40744-605">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-605">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="40744-606">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-606">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="40744-607">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-607">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="40744-608">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-608">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="40744-609">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-609">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="40744-610">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-610">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="40744-611">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-611">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="40744-612">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-612">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="40744-613">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-613">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="40744-614">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-614">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="40744-615">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="40744-615">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="40744-616">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="40744-616">Enable the IISIntegration components</span></span>

<span data-ttu-id="40744-617">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-617">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="40744-618">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-618">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="40744-619">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="40744-619">IIS options</span></span>

<span data-ttu-id="40744-620">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="40744-620">**In-process hosting model**</span></span>

<span data-ttu-id="40744-621">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-621">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="40744-622">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-622">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="40744-623">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-623">Option</span></span>                         | <span data-ttu-id="40744-624">기본</span><span class="sxs-lookup"><span data-stu-id="40744-624">Default</span></span> | <span data-ttu-id="40744-625">설정</span><span class="sxs-lookup"><span data-stu-id="40744-625">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-626">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-626">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-627">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-627">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-628">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-628">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-629">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-629">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-630">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-630">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="40744-631">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="40744-631">**Out-of-process hosting model**</span></span>

<span data-ttu-id="40744-632">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-632">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="40744-633">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-633">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="40744-634">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-634">Option</span></span>                         | <span data-ttu-id="40744-635">기본</span><span class="sxs-lookup"><span data-stu-id="40744-635">Default</span></span> | <span data-ttu-id="40744-636">설정</span><span class="sxs-lookup"><span data-stu-id="40744-636">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-637">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-637">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-638">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-638">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-639">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-639">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-640">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-640">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-641">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-641">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="40744-642">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="40744-642">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="40744-643">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="40744-643">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="40744-644">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-644">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="40744-645">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-645">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="40744-646">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-646">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="40744-647">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="40744-647">web.config file</span></span>

<span data-ttu-id="40744-648">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-648">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-649">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-649">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="40744-650">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="40744-650">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="40744-651">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-651">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="40744-652">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-652">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="40744-653">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-653">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="40744-654">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-654">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="40744-655">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-655">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="40744-656">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-656">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="40744-657">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-657">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="40744-658">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-658">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="40744-659">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-659">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="40744-660">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="40744-660">web.config file location</span></span>

<span data-ttu-id="40744-661">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-661">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="40744-662">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-662">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="40744-663">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-663">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="40744-664">중요한 파일은 *\<assembly>.runtimeconfig.json*, *\<assembly>.xml*(XML 문서 주석) 및 *\<assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-664">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="40744-665">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-665">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="40744-666">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-666">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="40744-667">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="40744-667">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="40744-668">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="40744-668">Transform web.config</span></span>

<span data-ttu-id="40744-669">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-669">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="40744-670">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-670">IIS configuration</span></span>

<span data-ttu-id="40744-671">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-671">**Windows Server operating systems**</span></span>

<span data-ttu-id="40744-672">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-672">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="40744-673">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-673">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="40744-674">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-674">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="40744-676">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-676">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="40744-677">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-677">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="40744-679">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-679">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-680">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-680">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="40744-681">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-681">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-682">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-682">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-683">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-683">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-684">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-684">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-685">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-685">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="40744-686">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-686">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-687">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-687">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-688">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-688">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="40744-689">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-689">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="40744-690">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-690">**Windows desktop operating systems**</span></span>

<span data-ttu-id="40744-691">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-691">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-692">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-692">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="40744-693">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-693">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="40744-694">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-694">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="40744-695">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-695">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="40744-696">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-696">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-697">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-697">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="40744-698">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-698">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-699">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-699">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="40744-700">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-700">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-701">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-701">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-702">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-702">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-703">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-703">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-704">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-704">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="40744-705">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-705">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-706">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-706">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-707">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-707">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="40744-709">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-709">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="40744-710">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-710">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="40744-711">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-711">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-712">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-712">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40744-713">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-713">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="40744-714">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-714">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="40744-715">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="40744-715">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="40744-716">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-716">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="40744-717">다운로드</span><span class="sxs-lookup"><span data-stu-id="40744-717">Download</span></span>

1. <span data-ttu-id="40744-718">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-718">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="40744-719">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-719">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="40744-720">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-720">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="40744-721">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-721">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="40744-722">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-722">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="40744-723">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-723">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="40744-724">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-724">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="40744-725">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-725">Run the installer on the server.</span></span> <span data-ttu-id="40744-726">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-726">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="40744-727">`OPT_NO_ANCM=1` &ndash; ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-727">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="40744-728">`OPT_NO_RUNTIME=1` &ndash; .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-728">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="40744-729">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-729">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-730">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-730">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="40744-731">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-731">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-732">`OPT_NO_X86=1` &ndash; x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-732">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="40744-733">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-733">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="40744-734">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-734">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="40744-735">`OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-735">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="40744-736">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="40744-736">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="40744-737">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-737">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="40744-738">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-738">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="40744-739">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-739">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="40744-740">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-740">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="40744-741">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-741">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="40744-742">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-742">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="40744-743">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-743">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="40744-744">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-744">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="40744-745">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-745">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="40744-746">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-746">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="40744-747">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="40744-747">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="40744-748">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-748">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="40744-749">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-749">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="40744-750">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-750">The preferred method is to use WebPI.</span></span> <span data-ttu-id="40744-751">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-751">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="40744-752">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="40744-752">Create the IIS site</span></span>

1. <span data-ttu-id="40744-753">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-753">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="40744-754">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-754">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="40744-755">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-755">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="40744-756">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-756">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="40744-757">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-757">Right-click the **Sites** folder.</span></span> <span data-ttu-id="40744-758">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-758">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-759">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-759">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="40744-760">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-760">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="40744-762">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-762">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="40744-763">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-763">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="40744-764">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-764">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="40744-765">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-765">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="40744-766">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="40744-766">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="40744-767">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-767">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="40744-768">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-768">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="40744-769">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-769">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-770">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-770">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="40744-772">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-772">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="40744-773">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-773">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="40744-774">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-774">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="40744-775">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-775">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="40744-776">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-776">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="40744-777">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-777">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="40744-778">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-778">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="40744-779">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-779">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="40744-780">애플리케이션 풀의 기본 ID(**프로세스 모델** > **ID**)가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-780">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="40744-781">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-781">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="40744-782">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-782">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="40744-783">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-783">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="40744-784">앱 배포</span><span class="sxs-lookup"><span data-stu-id="40744-784">Deploy the app</span></span>

<span data-ttu-id="40744-785">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-785">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="40744-786">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-786">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="40744-787">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-787">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="40744-788">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-788">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="40744-789">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-789">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="40744-791">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-791">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="40744-792">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-792">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="40744-793">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-793">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="40744-794">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="40744-794">Alternatives to Web Deploy</span></span>

<span data-ttu-id="40744-795">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-795">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="40744-796">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-796">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="40744-797">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="40744-797">Browse the website</span></span>

<span data-ttu-id="40744-798">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-798">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="40744-799">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-799">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="40744-800">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-800">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="40744-802">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="40744-802">Locked deployment files</span></span>

<span data-ttu-id="40744-803">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-803">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="40744-804">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-804">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="40744-805">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-805">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-806">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-806">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="40744-807">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-807">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="40744-808">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-808">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="40744-809">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-809">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="40744-810">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-810">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="40744-811">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="40744-811">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="40744-812">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="40744-812">Data protection</span></span>

<span data-ttu-id="40744-813">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-813">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="40744-814">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-814">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="40744-815">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-815">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="40744-816">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-816">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="40744-817">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-817">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="40744-818">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-818">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="40744-819">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-819">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="40744-820">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-820">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="40744-821">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-821">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-822">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="40744-822">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="40744-823">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-823">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="40744-824">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-824">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="40744-825">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-825">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="40744-826">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-826">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="40744-827">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-827">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="40744-828">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-828">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="40744-829">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-829">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="40744-830">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-830">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="40744-831">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-831">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="40744-832">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-832">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="40744-833">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-833">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="40744-834">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="40744-834">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="40744-835">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-835">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="40744-836">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-836">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="40744-837">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-837">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="40744-838">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-838">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="40744-839">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-839">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="40744-840">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-840">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="40744-841">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-841">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="40744-842">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-842">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="40744-843">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-843">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="40744-844">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-844">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="40744-845">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-845">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="40744-846">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-846">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="40744-847">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="40744-847">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="40744-848">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-848">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="40744-849">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-849">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="40744-850">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-850">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="40744-851">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-851">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="40744-852">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-852">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="40744-853">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-853">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="40744-854">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-854">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="40744-855">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="40744-855">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="40744-856">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-856">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="40744-857">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-857">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="40744-858">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="40744-858">Virtual Directories</span></span>

<span data-ttu-id="40744-859">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-859">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="40744-860">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-860">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="40744-861">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="40744-861">Sub-applications</span></span>

<span data-ttu-id="40744-862">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-862">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="40744-863">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-863">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="40744-864">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-864">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="40744-865">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-865">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="40744-866">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-866">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="40744-867">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-867">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="40744-868">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-868">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="40744-869">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-869">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="40744-870">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다. </span><span class="sxs-lookup"><span data-stu-id="40744-870">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="40744-871">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-871">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="40744-872">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-872">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="40744-873">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-873">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="40744-874">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-874">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="40744-875">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-875">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="40744-876">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-876">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="40744-877">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-877">Select **OK**.</span></span>

<span data-ttu-id="40744-878">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-878">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="40744-879">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-879">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="40744-880">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-880">Configuration of IIS with web.config</span></span>

<span data-ttu-id="40744-881">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-881">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="40744-882">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-882">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="40744-883">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-883">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="40744-884">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-884">For more information, see the following topics:</span></span>

* [<span data-ttu-id="40744-885">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="40744-885">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="40744-886">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-886">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="40744-887">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="40744-887">Configuration sections of web.config</span></span>

<span data-ttu-id="40744-888">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-888">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="40744-889">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-889">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="40744-890">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-890">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="40744-891">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="40744-891">Application Pools</span></span>

<span data-ttu-id="40744-892">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-892">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="40744-893">In-process 호스팅 &ndash; 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-893">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="40744-894">Out-of-process 호스팅 &ndash; 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-894">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="40744-895">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-895">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="40744-896">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-896">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="40744-897">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-897">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="40744-898">애플리케이션 풀 ID</span><span class="sxs-lookup"><span data-stu-id="40744-898">Application Pool Identity</span></span>

<span data-ttu-id="40744-899">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-899">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="40744-900">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-900">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="40744-901">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **ID**가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-901">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="40744-903">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-903">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="40744-904">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-904">Resources can be secured using this identity.</span></span> <span data-ttu-id="40744-905">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-905">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="40744-906">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-906">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="40744-907">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-907">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="40744-908">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-908">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="40744-909">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-909">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="40744-910">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-910">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="40744-911">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-911">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="40744-912">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-912">Select the **Check Names** button.</span></span> <span data-ttu-id="40744-913">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-913">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="40744-914">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-914">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="40744-915">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-915">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="40744-916">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-916">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="40744-918">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-918">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="40744-920">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-920">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="40744-921">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-921">Provide additional permissions as needed.</span></span>

<span data-ttu-id="40744-922">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-922">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="40744-923">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-923">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="40744-924">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-924">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="40744-925">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="40744-925">HTTP/2 support</span></span>

<span data-ttu-id="40744-926">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-926">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="40744-927">In-Process</span><span class="sxs-lookup"><span data-stu-id="40744-927">In-process</span></span>
  * <span data-ttu-id="40744-928">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="40744-928">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="40744-929">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="40744-929">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="40744-930">Out of Process</span><span class="sxs-lookup"><span data-stu-id="40744-930">Out-of-process</span></span>
  * <span data-ttu-id="40744-931">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="40744-931">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="40744-932">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-932">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="40744-933">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="40744-933">TLS 1.2 or later connection</span></span>

<span data-ttu-id="40744-934">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-934">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="40744-935">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-935">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="40744-936">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-936">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="40744-937">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-937">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="40744-938">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-938">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="40744-939">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-939">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="40744-940">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="40744-940">CORS preflight requests</span></span>

<span data-ttu-id="40744-941">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="40744-941">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="40744-942">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-942">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="40744-943">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-943">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="40744-944">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="40744-944">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="40744-945">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="40744-945">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="40744-946">[애플리케이션 초기화 모듈](#application-initialization-module)&ndash; 앱 호스팅 [In Process](#in-process-hosting-model) 또는 [Out of Process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-946">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="40744-947">[유휴 시간 제한](#idle-timeout)&ndash; - 앱 호스팅 [In Process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-947">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="40744-948">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="40744-948">Application Initialization Module</span></span>

<span data-ttu-id="40744-949">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’ </span><span class="sxs-lookup"><span data-stu-id="40744-949">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="40744-950">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-950">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="40744-951">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-951">The request triggers the app to start.</span></span> <span data-ttu-id="40744-952">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="40744-952">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="40744-953">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-953">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="40744-954">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="40744-954">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="40744-955">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-955">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="40744-956">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-956">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="40744-957">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-957">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="40744-958">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="40744-958">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="40744-959">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-959">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="40744-960">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-960">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="40744-961">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-961">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="40744-962">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-962">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="40744-963">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="40744-963">Using IIS Manager:</span></span>

  1. <span data-ttu-id="40744-964">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-964">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="40744-965">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-965">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="40744-966">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-966">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="40744-967">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-967">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="40744-968">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-968">Select **OK**.</span></span>
  1. <span data-ttu-id="40744-969">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-969">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="40744-970">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-970">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="40744-971">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-971">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="40744-972">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-972">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="40744-973">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-973">Select **OK**.</span></span>

* <span data-ttu-id="40744-974">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-974">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="40744-975">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="40744-975">Idle Timeout</span></span>

<span data-ttu-id="40744-976">‘앱 호스팅 In Process 에만 적용됩니다.’ </span><span class="sxs-lookup"><span data-stu-id="40744-976">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="40744-977">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-977">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="40744-978">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-978">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="40744-979">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-979">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="40744-980">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-980">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="40744-981">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-981">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="40744-982">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-982">Select **OK**.</span></span>
1. <span data-ttu-id="40744-983">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-983">Recycle the worker process.</span></span>

<span data-ttu-id="40744-984">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-984">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="40744-985">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-985">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="40744-986">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-986">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="40744-987">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="40744-987">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="40744-988">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="40744-988">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="40744-989">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="40744-989">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="40744-990">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="40744-990">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="40744-991">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="40744-991">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="40744-992">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="40744-992">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="40744-993">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="40744-993">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="40744-994">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="40744-994">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="40744-995">추가 자료</span><span class="sxs-lookup"><span data-stu-id="40744-995">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="40744-996">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="40744-996">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="40744-997">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="40744-997">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="40744-998">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="40744-998">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="40744-999">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-999">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="40744-1000">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-1000">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="40744-1001">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="40744-1001">Supported operating systems</span></span>

<span data-ttu-id="40744-1002">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1002">The following operating systems are supported:</span></span>

* <span data-ttu-id="40744-1003">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="40744-1003">Windows 7 or later</span></span>
* <span data-ttu-id="40744-1004">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="40744-1004">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="40744-1005">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1005">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="40744-1006">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1006">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="40744-1007">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1007">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="40744-1008">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="40744-1008">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="40744-1009">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="40744-1009">Supported platforms</span></span>

<span data-ttu-id="40744-1010">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1010">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="40744-1011">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1011">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="40744-1012">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1012">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="40744-1013">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1013">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="40744-1014">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1014">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="40744-1015">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1015">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="40744-1016">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1016">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="40744-1017">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1017">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="40744-1018">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](xref:fundamentals/servers/index#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-Process*) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1018">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="40744-1019">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1019">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="40744-1020">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1020">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="40744-1021">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1021">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="40744-1022">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1022">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="40744-1024">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1024">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="40744-1025">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1025">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="40744-1026">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1026">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="40744-1027">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1027">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="40744-1028">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1028">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="40744-1029">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1029">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="40744-1030">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1030">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="40744-1031">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1031">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="40744-1032">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1032">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="40744-1033">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1033">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="40744-1034">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1034">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="40744-1035">ASP.NET Core 모듈은 동적 포트를 생성하여 백 엔드 프로세스에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1035">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="40744-1036">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1036">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="40744-1037">`UseIISIntegration`은 localhost IP 주소(`127.0.0.1`)의 동적 포트에서 수신 대기하도록 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1037">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="40744-1038">동적 포트가 1234인 경우 Kestrel은 `127.0.0.1:1234`에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1038">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="40744-1039">이 구성은 다음에서 제공된 다른 URL 구성을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1039">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="40744-1040">Kestrel의 수신 대기 API</span><span class="sxs-lookup"><span data-stu-id="40744-1040">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="40744-1041">[구성](xref:fundamentals/configuration/index)(또는 [명령줄 --urls 옵션](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="40744-1041">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="40744-1042">모듈을 사용하는 경우 `UseUrls` 호출 또는 Kestrel의 `Listen` API가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1042">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="40744-1043">`UseUrls` 또는 `Listen`을 호출하는 경우 Kestrel은 IIS 없이 앱을 실행할 때만 지정된 포트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1043">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="40744-1044">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1044">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="40744-1045">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1045">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="40744-1046">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="40744-1046">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="40744-1047">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="40744-1047">Enable the IISIntegration components</span></span>

<span data-ttu-id="40744-1048">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1048">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="40744-1049">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1049">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="40744-1050">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="40744-1050">IIS options</span></span>

| <span data-ttu-id="40744-1051">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-1051">Option</span></span>                         | <span data-ttu-id="40744-1052">기본</span><span class="sxs-lookup"><span data-stu-id="40744-1052">Default</span></span> | <span data-ttu-id="40744-1053">설정</span><span class="sxs-lookup"><span data-stu-id="40744-1053">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-1054">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1054">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-1055">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1055">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-1056">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1056">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-1057">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1057">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-1058">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1058">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="40744-1059">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1059">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="40744-1060">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1060">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="40744-1061">옵션</span><span class="sxs-lookup"><span data-stu-id="40744-1061">Option</span></span>                         | <span data-ttu-id="40744-1062">기본</span><span class="sxs-lookup"><span data-stu-id="40744-1062">Default</span></span> | <span data-ttu-id="40744-1063">설정</span><span class="sxs-lookup"><span data-stu-id="40744-1063">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="40744-1064">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1064">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="40744-1065">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1065">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="40744-1066">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1066">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="40744-1067">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1067">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="40744-1068">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1068">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="40744-1069">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1069">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="40744-1070">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="40744-1070">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="40744-1071">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1071">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="40744-1072">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1072">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="40744-1073">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1073">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="40744-1074">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="40744-1074">web.config file</span></span>

<span data-ttu-id="40744-1075">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1075">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-1076">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1076">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="40744-1077">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1077">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="40744-1078">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1078">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="40744-1079">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1079">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="40744-1080">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1080">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="40744-1081">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1081">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="40744-1082">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1082">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="40744-1083">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1083">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="40744-1084">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1084">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="40744-1085">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1085">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="40744-1086">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1086">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="40744-1087">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="40744-1087">web.config file location</span></span>

<span data-ttu-id="40744-1088">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1088">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="40744-1089">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1089">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="40744-1090">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1090">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="40744-1091">중요한 파일은 *\<assembly>.runtimeconfig.json*, *\<assembly>.xml*(XML 문서 주석) 및 *\<assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1091">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="40744-1092">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1092">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="40744-1093">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1093">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="40744-1094">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="40744-1094">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="40744-1095">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="40744-1095">Transform web.config</span></span>

<span data-ttu-id="40744-1096">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1096">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="40744-1097">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-1097">IIS configuration</span></span>

<span data-ttu-id="40744-1098">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-1098">**Windows Server operating systems**</span></span>

<span data-ttu-id="40744-1099">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1099">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="40744-1100">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1100">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="40744-1101">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1101">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="40744-1103">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1103">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="40744-1104">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1104">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="40744-1106">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-1106">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-1107">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1107">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="40744-1108">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1108">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-1109">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1109">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-1110">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-1110">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-1111">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1111">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-1112">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1112">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="40744-1113">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1113">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-1114">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1114">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-1115">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1115">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="40744-1116">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1116">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="40744-1117">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="40744-1117">**Windows desktop operating systems**</span></span>

<span data-ttu-id="40744-1118">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1118">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-1119">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1119">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="40744-1120">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1120">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="40744-1121">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1121">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="40744-1122">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1122">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="40744-1123">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1123">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="40744-1124">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1124">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="40744-1125">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-1125">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="40744-1126">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1126">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="40744-1127">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1127">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="40744-1128">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1128">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="40744-1129">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-1129">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="40744-1130">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1130">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="40744-1131">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1131">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="40744-1132">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1132">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="40744-1133">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1133">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="40744-1134">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1134">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="40744-1136">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-1136">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="40744-1137">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1137">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="40744-1138">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1138">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="40744-1139">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1139">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40744-1140">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1140">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="40744-1141">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1141">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="40744-1142">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="40744-1142">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="40744-1143">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1143">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="40744-1144">다운로드</span><span class="sxs-lookup"><span data-stu-id="40744-1144">Download</span></span>

1. <span data-ttu-id="40744-1145">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1145">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="40744-1146">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1146">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="40744-1147">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1147">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="40744-1148">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1148">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="40744-1149">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1149">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="40744-1150">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1150">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="40744-1151">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="40744-1151">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="40744-1152">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1152">Run the installer on the server.</span></span> <span data-ttu-id="40744-1153">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1153">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="40744-1154">`OPT_NO_ANCM=1` &ndash; ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1154">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="40744-1155">`OPT_NO_RUNTIME=1` &ndash; .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1155">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="40744-1156">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1156">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-1157">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1157">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="40744-1158">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1158">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="40744-1159">`OPT_NO_X86=1` &ndash; x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1159">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="40744-1160">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1160">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="40744-1161">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1161">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="40744-1162">`OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1162">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="40744-1163">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="40744-1163">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="40744-1164">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1164">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="40744-1165">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1165">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="40744-1166">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1166">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="40744-1167">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1167">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="40744-1168">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1168">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="40744-1169">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1169">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="40744-1170">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1170">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="40744-1171">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1171">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="40744-1172">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1172">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="40744-1173">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1173">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="40744-1174">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="40744-1174">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="40744-1175">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1175">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="40744-1176">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1176">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="40744-1177">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1177">The preferred method is to use WebPI.</span></span> <span data-ttu-id="40744-1178">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1178">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="40744-1179">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="40744-1179">Create the IIS site</span></span>

1. <span data-ttu-id="40744-1180">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1180">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="40744-1181">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1181">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="40744-1182">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1182">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="40744-1183">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1183">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="40744-1184">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1184">Right-click the **Sites** folder.</span></span> <span data-ttu-id="40744-1185">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1185">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-1186">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1186">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="40744-1187">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1187">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="40744-1189">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1189">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="40744-1190">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1190">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="40744-1191">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1191">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="40744-1192">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1192">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="40744-1193">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="40744-1193">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="40744-1194">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1194">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="40744-1195">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1195">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="40744-1196">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1196">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="40744-1197">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1197">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="40744-1199">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1199">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="40744-1200">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1200">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="40744-1201">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1201">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="40744-1202">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1202">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="40744-1203">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1203">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="40744-1204">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1204">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="40744-1205">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1205">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="40744-1206">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1206">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="40744-1207">애플리케이션 풀의 기본 ID(**프로세스 모델** > **ID**)가 **ApplicationPoolIdentity**에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1207">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="40744-1208">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1208">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="40744-1209">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="40744-1209">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="40744-1210">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1210">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="40744-1211">앱 배포</span><span class="sxs-lookup"><span data-stu-id="40744-1211">Deploy the app</span></span>

<span data-ttu-id="40744-1212">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1212">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="40744-1213">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1213">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="40744-1214">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-1214">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="40744-1215">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1215">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="40744-1216">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1216">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="40744-1218">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="40744-1218">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="40744-1219">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1219">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="40744-1220">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1220">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="40744-1221">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="40744-1221">Alternatives to Web Deploy</span></span>

<span data-ttu-id="40744-1222">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1222">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="40744-1223">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1223">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="40744-1224">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="40744-1224">Browse the website</span></span>

<span data-ttu-id="40744-1225">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1225">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="40744-1226">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1226">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="40744-1227">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1227">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="40744-1229">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="40744-1229">Locked deployment files</span></span>

<span data-ttu-id="40744-1230">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1230">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="40744-1231">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1231">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="40744-1232">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1232">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-1233">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1233">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="40744-1234">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1234">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="40744-1235">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1235">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="40744-1236">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1236">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="40744-1237">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1237">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="40744-1238">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="40744-1238">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="40744-1239">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="40744-1239">Data protection</span></span>

<span data-ttu-id="40744-1240">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1240">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="40744-1241">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1241">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="40744-1242">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1242">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="40744-1243">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1243">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="40744-1244">모든 쿠키 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1244">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="40744-1245">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1245">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="40744-1246">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1246">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="40744-1247">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData 쿠키](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1247">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="40744-1248">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1248">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="40744-1249">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="40744-1249">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="40744-1250">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1250">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="40744-1251">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1251">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="40744-1252">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1252">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="40744-1253">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1253">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="40744-1254">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1254">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="40744-1255">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1255">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="40744-1256">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1256">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="40744-1257">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1257">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="40744-1258">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1258">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="40744-1259">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1259">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="40744-1260">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1260">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="40744-1261">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="40744-1261">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="40744-1262">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1262">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="40744-1263">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1263">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="40744-1264">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1264">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="40744-1265">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1265">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="40744-1266">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1266">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="40744-1267">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1267">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="40744-1268">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1268">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="40744-1269">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1269">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="40744-1270">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1270">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="40744-1271">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1271">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="40744-1272">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1272">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="40744-1273">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1273">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="40744-1274">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="40744-1274">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="40744-1275">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1275">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="40744-1276">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1276">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="40744-1277">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1277">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="40744-1278">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1278">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="40744-1279">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1279">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="40744-1280">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1280">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="40744-1281">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1281">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="40744-1282">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="40744-1282">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="40744-1283">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1283">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="40744-1284">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1284">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="40744-1285">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="40744-1285">Virtual Directories</span></span>

<span data-ttu-id="40744-1286">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1286">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="40744-1287">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1287">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="40744-1288">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="40744-1288">Sub-applications</span></span>

<span data-ttu-id="40744-1289">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1289">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="40744-1290">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1290">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="40744-1291">하위 앱에는 ASP.NET Core 모듈이 처리기로 포함되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1291">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="40744-1292">하위 앱의 *web.config* 파일에 모듈이 처리기로 추가되면, 하위 앱을 찾으려고 할 때 잘못된 구성 파일을 참조하는 *500.19 내부 서버 오류*가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1292">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="40744-1293">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1293">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="40744-1294">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하는 경우, 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1294">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="40744-1295">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1295">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="40744-1296">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1296">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="40744-1297">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1297">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="40744-1298">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1298">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="40744-1299">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1299">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="40744-1300">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1300">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="40744-1301">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다. </span><span class="sxs-lookup"><span data-stu-id="40744-1301">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="40744-1302">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1302">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="40744-1303">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1303">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="40744-1304">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1304">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="40744-1305">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1305">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="40744-1306">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1306">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="40744-1307">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1307">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="40744-1308">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1308">Select **OK**.</span></span>

<span data-ttu-id="40744-1309">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1309">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="40744-1310">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1310">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="40744-1311">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="40744-1311">Configuration of IIS with web.config</span></span>

<span data-ttu-id="40744-1312">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1312">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="40744-1313">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1313">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="40744-1314">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1314">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="40744-1315">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1315">For more information, see the following topics:</span></span>

* [<span data-ttu-id="40744-1316">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="40744-1316">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="40744-1317">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1317">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="40744-1318">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="40744-1318">Configuration sections of web.config</span></span>

<span data-ttu-id="40744-1319">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1319">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="40744-1320">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1320">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="40744-1321">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1321">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="40744-1322">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="40744-1322">Application Pools</span></span>

<span data-ttu-id="40744-1323">서버에서 여러 웹 사이트를 호스트하는 경우 각 앱을 해당 앱 풀에서 실행하여 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1323">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="40744-1324">이 구성은 IIS **웹 사이트 추가** 대화 상자의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1324">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="40744-1325">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1325">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="40744-1326">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1326">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="40744-1327">애플리케이션 풀 ID</span><span class="sxs-lookup"><span data-stu-id="40744-1327">Application Pool Identity</span></span>

<span data-ttu-id="40744-1328">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1328">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="40744-1329">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1329">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="40744-1330">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **ID**가 **ApplicationPoolIdentity**를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1330">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="40744-1332">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1332">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="40744-1333">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1333">Resources can be secured using this identity.</span></span> <span data-ttu-id="40744-1334">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1334">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="40744-1335">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1335">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="40744-1336">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1336">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="40744-1337">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1337">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="40744-1338">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1338">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="40744-1339">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1339">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="40744-1340">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1340">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="40744-1341">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1341">Select the **Check Names** button.</span></span> <span data-ttu-id="40744-1342">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1342">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="40744-1343">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1343">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="40744-1344">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1344">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="40744-1345">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1345">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="40744-1347">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1347">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="40744-1349">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1349">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="40744-1350">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1350">Provide additional permissions as needed.</span></span>

<span data-ttu-id="40744-1351">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1351">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="40744-1352">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1352">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="40744-1353">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1353">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="40744-1354">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="40744-1354">HTTP/2 support</span></span>

<span data-ttu-id="40744-1355">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음 기본 요구 사항을 충족하는 Out of Process 배포에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1355">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="40744-1356">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="40744-1356">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="40744-1357">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1357">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="40744-1358">대상 프레임워크: HTTP/2 연결은 IIS에 의해 완전히 처리되므로 Out of Process 배포에는 해당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1358">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="40744-1359">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="40744-1359">TLS 1.2 or later connection</span></span>

<span data-ttu-id="40744-1360">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1360">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="40744-1361">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1361">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="40744-1362">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1362">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="40744-1363">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1363">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="40744-1364">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="40744-1364">CORS preflight requests</span></span>

<span data-ttu-id="40744-1365">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="40744-1365">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="40744-1366">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="40744-1366">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="40744-1367">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="40744-1367">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="40744-1368">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="40744-1368">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="40744-1369">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="40744-1369">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="40744-1370">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="40744-1370">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="40744-1371">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="40744-1371">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="40744-1372">추가 자료</span><span class="sxs-lookup"><span data-stu-id="40744-1372">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="40744-1373">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="40744-1373">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="40744-1374">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="40744-1374">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="40744-1375">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="40744-1375">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
