---
title: "제목: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: guardrex description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239389"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="eb584-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="eb584-103">'Blazor'</span></span>

<span data-ttu-id="eb584-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="eb584-104">'Identity'</span></span>

<span data-ttu-id="eb584-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="eb584-105">'Let's Encrypt'</span></span>

* <span data-ttu-id="eb584-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="eb584-106">'Razor'</span></span>
* <span data-ttu-id="eb584-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span><span class="sxs-lookup"><span data-stu-id="eb584-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span></span>

<span data-ttu-id="eb584-108">ASP.NET Core Blazor WebAssembly 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="eb584-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

* <span data-ttu-id="eb584-109">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) 및 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="eb584-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span> <span data-ttu-id="eb584-110">[Blazor WebAssembly 호스팅 모델 사용](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="eb584-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>
* <span data-ttu-id="eb584-111">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 병렬로 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span> <span data-ttu-id="eb584-112">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-112">The app is executed directly on the browser UI thread.</span></span>

## <a name="precompression"></a><span data-ttu-id="eb584-113">다음 배포 전략이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-113">The following deployment strategies are supported:</span></span>

<span data-ttu-id="eb584-114">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="eb584-115">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>

* <span data-ttu-id="eb584-116">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span>
* <span data-ttu-id="eb584-117">이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

<span data-ttu-id="eb584-118">사전 압축</span><span class="sxs-lookup"><span data-stu-id="eb584-118">Precompression</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="eb584-119">Blazor WebAssembly 앱이 게시될 때 출력을 사전 압축하여 앱의 크기를 줄이고 런타임 압축이 필요 없게 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-119">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="eb584-120">다음 압축 알고리즘이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-120">The following compression algorithms are used:</span></span>

<span data-ttu-id="eb584-121">[Brotli](https://tools.ietf.org/html/rfc7932)(최고 수준)</span><span class="sxs-lookup"><span data-stu-id="eb584-121">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span> [<span data-ttu-id="eb584-122">Gzip</span><span class="sxs-lookup"><span data-stu-id="eb584-122">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

* <span data-ttu-id="eb584-123">압축을 해제하려면 앱의 프로젝트 파일에 `BlazorEnableCompression` MSBuild 속성을 추가하고 값을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-123">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>
* <span data-ttu-id="eb584-124">IIS *web.config* 압축 구성에 대해서는 [IIS: Brotli 및 Gzip 압축](#brotli-and-gzip-compression) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-124">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

<span data-ttu-id="eb584-125">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="eb584-125">Rewrite URLs for correct routing</span></span>

1. <span data-ttu-id="eb584-126">Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor 서버에서 호스트한 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-126">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span>
1. <span data-ttu-id="eb584-127">다음 두 가지 구성 요소가 있는 Blazor WebAssembly 앱을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-127">Consider a Blazor WebAssembly app with two components:</span></span>
1. <span data-ttu-id="eb584-128">*Main.razor*: 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-128">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
1. <span data-ttu-id="eb584-129">*About.razor*: `About` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-129">*About.razor*: `About` component.</span></span>

<span data-ttu-id="eb584-130">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="eb584-130">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span> <span data-ttu-id="eb584-131">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-131">The browser makes a request.</span></span> <span data-ttu-id="eb584-132">기본 페이지(일반적으로 *index.html*)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-132">The default page is returned, which is usually *index.html*.</span></span>

<span data-ttu-id="eb584-133">*index.html* 앱을 부트스트랩으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-133">*index.html* bootstraps the app.</span></span> Blazor<span data-ttu-id="eb584-134">의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-134">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="eb584-135">Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-135">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="eb584-136">*Blazor WebAssembly 앱 내*의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-136">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span>

<span data-ttu-id="eb584-137">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-137">The router handles the requests internally.</span></span> <span data-ttu-id="eb584-138">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-138">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="eb584-139">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-139">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="eb584-140">브라우저는 인터넷 기반 호스트에 대해 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 *index.html* 페이지에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-140">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span>

<span data-ttu-id="eb584-141">*index.html*이 반환되는 경우 앱의 Blazor 라우터가 인수하여 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-141">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span> <span data-ttu-id="eb584-142">IIS 서버에 배포하는 경우 앱의 게시된 *web.config* 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-142">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="eb584-143">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-143">For more information, see the [IIS](#iis) section.</span></span> <span data-ttu-id="eb584-144">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="eb584-144">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="eb584-145">*호스트된 배포*는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-145">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="eb584-146">클라이언트 Blazor WebAssembly 앱이 서버 앱의 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-146">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="eb584-147">두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-147">The two apps are deployed together.</span></span>

<span data-ttu-id="eb584-148">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-148">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="eb584-149">호스트된 배포의 경우 Visual Studio에는 **Hosted**(`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-149">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="eb584-150">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-150">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="eb584-151">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-151">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="eb584-152">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="eb584-152">Standalone deployment</span></span>

<span data-ttu-id="eb584-153">*독립 실행형 배포*는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-153">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="eb584-154">모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-154">Any static file server is able to serve the Blazor app.</span></span> <span data-ttu-id="eb584-155">독립 실행형 배포 자산은 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-155">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="eb584-156">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="eb584-156">Azure App Service</span></span>

Blazor<span data-ttu-id="eb584-157"> WebAssembly 앱은 [IIS](#iis)에서 앱을 호스트하는 Windows의 Azure App Services에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-157"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span> <span data-ttu-id="eb584-158">Linux용 Azure App Service에 독립 실행형 Blazor WebAssembly 앱 배포는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-158">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span>

<span data-ttu-id="eb584-159">지금은 앱을 호스트하는 Linux 서버 이미지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-159">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="eb584-160">이 시나리오를 사용할 수 있도록 하는 작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-160">Work is in progress to enable this scenario.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="eb584-161">IIS</span><span class="sxs-lookup"><span data-stu-id="eb584-161">IIS</span></span>

<span data-ttu-id="eb584-162">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-162">IIS is a capable static file server for Blazor apps.</span></span>

* <span data-ttu-id="eb584-163">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-163">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>
  * <span data-ttu-id="eb584-164">게시된 자산은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-164">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>
  * <span data-ttu-id="eb584-165">웹 서버 또는 호스팅 서비스에서 *publish* 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-165">Host the contents of the *publish* folder on the web server or hosting service.</span></span>
  * <span data-ttu-id="eb584-166">web.config</span><span class="sxs-lookup"><span data-stu-id="eb584-166">web.config</span></span>
  * <span data-ttu-id="eb584-167">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 *web.config* 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-167">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>
  * <span data-ttu-id="eb584-168">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-168">MIME types are set for the following file extensions:</span></span>
* <span data-ttu-id="eb584-169">*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="eb584-169">*.dll*: `application/octet-stream`</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="eb584-170">*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="eb584-170">*.json*: `application/json`</span></span>
  * <span data-ttu-id="eb584-171">*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="eb584-171">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="eb584-172">*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="eb584-172">*.woff*: `application/font-woff`</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="eb584-173">*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="eb584-173">*.woff2*: `application/font-woff`</span></span>

<span data-ttu-id="eb584-174">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-174">HTTP compression is enabled for the following MIME types:</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="eb584-175">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-175">URL Rewrite Module rules are established:</span></span>

<span data-ttu-id="eb584-176">앱의 정적 자산이 상주하는 하위 디렉터리(*wwwroot/{PATH REQUESTED}* )를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-176">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span> <span data-ttu-id="eb584-177">파일이 아닌 자산에 대한 요청이 정적 자산 폴더에 있는 앱의 기본 문서(*wwwroot/index.html*)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-177">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span> <span data-ttu-id="eb584-178">사용자 지정 web.config 사용</span><span class="sxs-lookup"><span data-stu-id="eb584-178">Use a custom web.config</span></span> <span data-ttu-id="eb584-179">사용자 지정 *web.config* 파일을 사용하려면 사용자 지정 *web.config* 파일을 프로젝트 폴더의 루트에 배치하고 프로젝트를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-179">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

1. <span data-ttu-id="eb584-180">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="eb584-180">Install the URL Rewrite Module</span></span> <span data-ttu-id="eb584-181">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-181">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="eb584-182">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-182">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span>
1. <span data-ttu-id="eb584-183">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-183">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="eb584-184">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-184">Use the Web Platform Installer to install the module:</span></span> <span data-ttu-id="eb584-185">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-185">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="eb584-186">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-186">For the English version, select **WebPI** to download the WebPI installer.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="eb584-187">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-187">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>

<span data-ttu-id="eb584-188">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-188">Copy the installer to the server.</span></span> <span data-ttu-id="eb584-189">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-189">Run the installer.</span></span>

* <span data-ttu-id="eb584-190">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-190">Select the **Install** button and accept the license terms.</span></span>
* <span data-ttu-id="eb584-191">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-191">A server restart isn't required after the install completes.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="eb584-192">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="eb584-192">Configure the website</span></span>

<span data-ttu-id="eb584-193">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-193">Set the website's **Physical path** to the app's folder.</span></span>

* <span data-ttu-id="eb584-194">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-194">The folder contains:</span></span>

  <span data-ttu-id="eb584-195">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 *web.config* 파일.</span><span class="sxs-lookup"><span data-stu-id="eb584-195">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="eb584-196">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-196">The app's static asset folder.</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="eb584-197">IIS 하위 앱으로 호스트</span><span class="sxs-lookup"><span data-stu-id="eb584-197">Host as an IIS sub-app</span></span> <span data-ttu-id="eb584-198">독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-198">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="eb584-199">상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-199">Disable the inherited ASP.NET Core Module handler.</span></span>

<span data-ttu-id="eb584-200">Blazor 앱의 게시된 *web.config* 파일에 `<handlers>` 섹션을 추가하여 파일에서 핸들러를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-200">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span> <span data-ttu-id="eb584-201">`inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-201">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="eb584-202">처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:host-and-deploy/blazor/index#app-base-path)에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-202">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

<span data-ttu-id="eb584-203">IIS에서 하위 앱을 구성할 때
앱의 *index.html* 파일에서 앱 기본 경로를 사용한 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-203">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span> <span data-ttu-id="eb584-204">Brotli 및 Gzip 압축</span><span class="sxs-lookup"><span data-stu-id="eb584-204">Brotli and Gzip compression</span></span> <span data-ttu-id="eb584-205">*web.config*를 통해 IIS를 구성하여 Brotli 또는 Gzip 압축 Blazor 자산을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-205">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span>

<span data-ttu-id="eb584-206">예제 구성은 [web.config](webassembly/_samples/web.config?raw=true)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-206">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

### <a name="azure-storage"></a><span data-ttu-id="eb584-207">문제 해결</span><span class="sxs-lookup"><span data-stu-id="eb584-207">Troubleshooting</span></span>

<span data-ttu-id="eb584-208">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-208">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="eb584-209">모듈이 설치되지 않은 경우 IIS가 *web.config* 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-209">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span>

<span data-ttu-id="eb584-210">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-210">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

* <span data-ttu-id="eb584-211">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-211">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>
* <span data-ttu-id="eb584-212">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="eb584-212">Azure Storage</span></span> <span data-ttu-id="eb584-213">[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-213">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="eb584-214">사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-214">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span> <span data-ttu-id="eb584-215">스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="eb584-215">When the blob service is enabled for static website hosting on a storage account:</span></span>

<span data-ttu-id="eb584-216">**인덱스 문서 이름**을 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-216">Set the **Index document name** to `index.html`.</span></span>

* <span data-ttu-id="eb584-217">**오류 문서 경로**를 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-217">Set the **Error document path** to `index.html`.</span></span>
* Razor<span data-ttu-id="eb584-218"> 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob 서비스에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-218"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span>

  <span data-ttu-id="eb584-219">이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-219">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span>
  
  1. <span data-ttu-id="eb584-220">*index.html* BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-220">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>
  1. <span data-ttu-id="eb584-221">파일의 `Content-Type` 헤더에 부적절한 MIME 형식으로 인해 런타임에 파일이 로드되지 않을 경우 다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-221">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

<span data-ttu-id="eb584-222">파일이 배포될 때 올바른 MIME 형식(`Content-Type` 헤더)을 설정하도록 도구를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-222">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>

### <a name="nginx"></a><span data-ttu-id="eb584-223">앱이 배포된 후 파일에 대한 MIME 형식(`Content-Type` 헤더)을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-223">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

<span data-ttu-id="eb584-224">각 파일의 스토리지 탐색기(Azure Portal)에서</span><span class="sxs-lookup"><span data-stu-id="eb584-224">In Storage Explorer (Azure portal) for each file:</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="eb584-225">파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-225">Right-click the file and select **Properties**.</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="eb584-226">**ContentType**을 설정하고 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-226">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="eb584-227">자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-227">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span> <span data-ttu-id="eb584-228">Nginx</span><span class="sxs-lookup"><span data-stu-id="eb584-228">Nginx</span></span>

<span data-ttu-id="eb584-229">다음 *nginx.conf* 파일은 Nginx가 디스크에서 *index.html* 파일을 찾을 수 없을 때마다 해당 파일을 보내도록 구성하는 방법을 보여 주도록 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-229">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="eb584-230">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-230">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

<span data-ttu-id="eb584-231">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="eb584-231">Nginx in Docker</span></span>

1. <span data-ttu-id="eb584-232">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-232">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="eb584-233">Dockerfile을 업데이트하여 *nginx.config* 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-233">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="eb584-234">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-234">Add one line to the Dockerfile, as shown in the following example:</span></span>

1. <span data-ttu-id="eb584-235">Apache</span><span class="sxs-lookup"><span data-stu-id="eb584-235">Apache</span></span>

1. <span data-ttu-id="eb584-236">Blazor WebAssembly 앱을 CentOS 7 이상에 배포하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-236">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

<span data-ttu-id="eb584-237">Apache 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-237">Create the Apache configuration file.</span></span>

### <a name="github-pages"></a><span data-ttu-id="eb584-238">다음 예제는 단순화된 구성 파일입니다(*blazorapp.config*).</span><span class="sxs-lookup"><span data-stu-id="eb584-238">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

<span data-ttu-id="eb584-239">Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-239">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span> <span data-ttu-id="eb584-240">앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-240">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span> <span data-ttu-id="eb584-241">Apache 서비스를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-241">Restart the Apache service.</span></span>

<span data-ttu-id="eb584-242">자세한 내용은 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-242">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span> <span data-ttu-id="eb584-243">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="eb584-243">GitHub Pages</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="eb584-244">URL 다시 생성을 처리하려면 *index.html* 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 *404.html* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-244">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span>

<span data-ttu-id="eb584-245">커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](https://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-245">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span>

### <a name="content-root"></a><span data-ttu-id="eb584-246">커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-246">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="eb584-247">조직의 사이트 대신 프로젝트 사이트를 사용하는 경우 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-247">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="eb584-248">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="eb584-248">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

* <span data-ttu-id="eb584-249">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="eb584-249">Host configuration values</span></span> <span data-ttu-id="eb584-250">[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-250">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="eb584-251">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="eb584-251">Content root</span></span> <span data-ttu-id="eb584-252">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-252">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="eb584-253">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-253">In the following examples, `/content-root-path` is the app's content root path.</span></span> <span data-ttu-id="eb584-254">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-254">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="eb584-255">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-255">From the app's directory, execute:</span></span>

<span data-ttu-id="eb584-256">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="eb584-257">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-257">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="eb584-258">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="eb584-259">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eb584-260">경로 기준</span><span class="sxs-lookup"><span data-stu-id="eb584-260">Path base</span></span>

* <span data-ttu-id="eb584-261">`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.)</span><span class="sxs-lookup"><span data-stu-id="eb584-261">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="eb584-262">다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-262">In the following examples, `/relative-URL-path` is the app's path base.</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="eb584-263">자세한 내용은 [앱 기본 경로](xref:host-and-deploy/blazor/index#app-base-path)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-263">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="eb584-264">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-264">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="eb584-265">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="eb584-265">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span> <span data-ttu-id="eb584-266">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-266">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="eb584-267">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-267">From the app's directory, execute:</span></span>

<span data-ttu-id="eb584-268">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-268">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span>

* <span data-ttu-id="eb584-269">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-269">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="eb584-270">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-270">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="eb584-271">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-271">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eb584-272">URL</span><span class="sxs-lookup"><span data-stu-id="eb584-272">URLs</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="eb584-273">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-273">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span> <span data-ttu-id="eb584-274">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-274">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="eb584-275">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-275">From the app's directory, execute:</span></span>

<span data-ttu-id="eb584-276">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-276">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="eb584-277">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-277">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="eb584-278">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-278">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

<span data-ttu-id="eb584-279">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-279">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="eb584-280">링커 구성</span><span class="sxs-lookup"><span data-stu-id="eb584-280">Configure the Linker</span></span>

* Blazor<span data-ttu-id="eb584-281">는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-281"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span>
* <span data-ttu-id="eb584-282">자세한 내용은 <xref:host-and-deploy/blazor/configure-linker>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-282">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
* <span data-ttu-id="eb584-283">사용자 지정 부팅 리소스 로드</span><span class="sxs-lookup"><span data-stu-id="eb584-283">Custom boot resource loading</span></span>

<span data-ttu-id="eb584-284">`loadBootResource` 함수로 Blazor WebAssembly 앱을 초기화하여 기본 제공 부팅 리소스 로드 메커니즘을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-284">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span>

| <span data-ttu-id="eb584-285">다음 시나리오에 `loadBootResource`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-285">Use `loadBootResource` for the following scenarios:</span></span>    | <span data-ttu-id="eb584-286">사용자가 CDN에서 표준 시간대 데이터 또는 *dotnet.wasm* 같은 정적 리소스를 로드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-286">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="eb584-287">HTTP 요청을 사용하여 압축된 어셈블리를 로드하고 서버에서 압축된 콘텐츠 페치를 지원하지 않는 호스트의 경우 클라이언트에서 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-287">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span> <span data-ttu-id="eb584-288">각 `fetch` 요청을 새 이름으로 리디렉션하여 리소스 별칭을 다른 이름으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-288">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span> |
| `name`       | <span data-ttu-id="eb584-289">`loadBootResource` 매개 변수는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-289">`loadBootResource` parameters appear in the following table.</span></span> |
| `defaultUri` | <span data-ttu-id="eb584-290">매개 변수</span><span class="sxs-lookup"><span data-stu-id="eb584-290">Parameter</span></span> |
| `integrity`  | <span data-ttu-id="eb584-291">Description</span><span class="sxs-lookup"><span data-stu-id="eb584-291">Description</span></span> |

<span data-ttu-id="eb584-292">리소스 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-292">The type of the resource.</span></span>

* <span data-ttu-id="eb584-293">허용되는 형식: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="eb584-293">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> <span data-ttu-id="eb584-294">리소스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-294">The name of the resource.</span></span>

  * <span data-ttu-id="eb584-295">리소스의 상대 또는 절대 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-295">The relative or absolute URI of the resource.</span></span>
  * <span data-ttu-id="eb584-296">응답에서 예상되는 콘텐츠를 나타내는 무결성 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-296">The integrity string representing the expected content in the response.</span></span>
  * <span data-ttu-id="eb584-297">`loadBootResource`는 로드 프로세스를 재정의하기 위해 다음 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-297">`loadBootResource` returns any of the following to override the loading process:</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="eb584-298">URI 문자열.</span><span class="sxs-lookup"><span data-stu-id="eb584-298">URI string.</span></span> <span data-ttu-id="eb584-299">다음 예제(*wwwroot/index.html*)에서 다음 파일은 `https://my-awesome-cdn.com/`의 CDN에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-299">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  <span data-ttu-id="eb584-300">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="eb584-300">*dotnet.\*.js*</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="eb584-301">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="eb584-301">*dotnet.wasm*</span></span>

<span data-ttu-id="eb584-302">표준 시간대 데이터</span><span class="sxs-lookup"><span data-stu-id="eb584-302">Timezone data</span></span> <span data-ttu-id="eb584-303">`Promise<Response>`입니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-303">`Promise<Response>`.</span></span>

<span data-ttu-id="eb584-304">헤더에서 `integrity` 매개 변수를 전달하여 기본 무결성 검사 동작을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span> <span data-ttu-id="eb584-305">다음 예제(*wwwroot/index.html*)에서는 사용자 지정 HTTP 헤더를 아웃바운드 요청에 추가하고 `integrity` 매개 변수를 `fetch` 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-305">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="eb584-306">`null`/`undefined`, 기본 로드 동작이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="eb584-307">외부 소스는 브라우저에서 원본 간 리소스 로드를 허용하는 데 필요한 CORS 헤더를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span>

<span data-ttu-id="eb584-308">일반적으로 CDN은 필요한 헤더를 기본적으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-308">CDNs usually provide the required headers by default.</span></span> <span data-ttu-id="eb584-309">사용자 지정 동작의 경우에만 형식을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-309">You only need to specify types for custom behaviors.</span></span>

<span data-ttu-id="eb584-310">`loadBootResource`에 지정되지 않은 형식은 기본 로드 동작에 따라 프레임워크에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

<span data-ttu-id="eb584-311">DLL 파일의 파일 이름 확장명 변경</span><span class="sxs-lookup"><span data-stu-id="eb584-311">Change the filename extension of DLL files</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="eb584-312">앱 게시 *.dll* 파일의 파일 이름 확장명을 변경해야 하는 경우 이 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="eb584-312">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="eb584-313">앱을 게시한 후 셸 스크립트 또는 DevOps 빌드 파이프라인을 사용하여 다른 파일 확장명을 사용하도록 *.dll* 파일의 이름을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-313">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="eb584-314">앱 게시 출력(예: *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*)의 *wwwroot* 디렉터리에 있는 *.dll* 파일을 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-314">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="eb584-315">다음 예제에서는 *.bin* 파일 확장명을 사용하도록 *.dll* 파일의 이름이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-315">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="eb584-316">Windows에서:</span><span class="sxs-lookup"><span data-stu-id="eb584-316">On Windows:</span></span>

* <span data-ttu-id="eb584-317">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-317">If service worker assets are also in use, add the following command:</span></span> <span data-ttu-id="eb584-318">Linux 또는 macOS에서:</span><span class="sxs-lookup"><span data-stu-id="eb584-318">On Linux or macOS:</span></span>
* <span data-ttu-id="eb584-319">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-319">If service worker assets are also in use, add the following command:</span></span>

<span data-ttu-id="eb584-320">*.bin* 이외의 다른 파일 확장명을 사용하려면 이전 명령에서 *.bin*을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-320">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span> <span data-ttu-id="eb584-321">압축된 *blazor.boot.json.gz* 및 *blazor.boot.json.br* 파일을 처리하려면 다음 접근 방법 중 하나를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-321">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span> <span data-ttu-id="eb584-322">압축된 *blazor.boot.json.gz* 및 *blazor.boot.json.br* 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-322">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span>

<span data-ttu-id="eb584-323">압축은 이 접근 방법으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-323">Compression is disabled with this approach.</span></span>

<span data-ttu-id="eb584-324">업데이트된 *blazor.boot.json* 파일을 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-324">Recompress the updated *blazor.boot.json* file.</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="eb584-325">위의 지침은 서비스 작업자 자산을 사용 중인 경우에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-325">The preceding guidance also applies when service worker assets are in use.</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="eb584-326">*wwwroot/service-worker-assets.js.br* 및 *wwwroot/service-worker-assets.js.gz*를 제거하거나 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-326">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="eb584-327">그렇지 않으면 브라우저에서 파일 무결성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="eb584-327">Otherwise, file integrity checks fail in the browser.</span></span>
 
