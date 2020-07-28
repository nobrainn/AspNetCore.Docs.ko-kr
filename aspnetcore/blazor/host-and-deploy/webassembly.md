---
title: ASP.NET Core 호스트 및 배포 Blazor WebAssembly
author: guardrex
description: ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2a2b0dabc26c14624144ce7eceb5861fe56f1054
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445140"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="c25d4-103">ASP.NET Core 호스트 및 배포 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c25d4-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c25d4-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) 및 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="c25d4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="c25d4-105">[Blazor WebAssembly 호스팅 모델](xref:blazor/hosting-models#blazor-webassembly)을 사용하면 다음과 같이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="c25d4-106">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 병렬로 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="c25d4-107">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="c25d4-108">다음 배포 전략이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="c25d4-109">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c25d4-110">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="c25d4-111">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="c25d4-112">이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="c25d4-113">압축</span><span class="sxs-lookup"><span data-stu-id="c25d4-113">Compression</span></span>

<span data-ttu-id="c25d4-114">Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="c25d4-115">다음 압축 알고리즘이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="c25d4-116">[Brotli](https://tools.ietf.org/html/rfc7932)(최고 수준)</span><span class="sxs-lookup"><span data-stu-id="c25d4-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="c25d4-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="c25d4-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="c25d4-118">Blazor는 호스트를 사용하여 적절한 압축 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="c25d4-119">ASP.NET Core 호스트 프로젝트를 사용하는 경우 호스트 프로젝트는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="c25d4-120">Blazor WebAssembly 독립 실행형 앱을 호스트하는 경우 정적으로 압축된 파일이 제공되도록 추가 작업이 필요할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="c25d4-121">IIS `web.config` 압축 구성에 대해서는 [IIS: Brotli 및 Gzip 압축](#brotli-and-gzip-compression) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="c25d4-122">GitHub 페이지와 같이 정적으로 압축된 파일 콘텐츠 협상을 지원하지 않는 정적 호스팅 솔루션에서 호스트하는 경우 Brotli로 압축된 파일을 가져와 디코딩하는 앱을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="c25d4-123">앱의 [google/brotli GitHub 리포지토리](https://github.com/google/brotli/)에서 Brotli 디코더를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="c25d4-124">디코더를 사용하도록 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-124">Update the app to use the decoder.</span></span> <span data-ttu-id="c25d4-125">`wwwroot/index.html`에서 닫는 `<body>` 태그 내부의 태그를 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
              'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
              { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
    </script>
    ```
 
<span data-ttu-id="c25d4-126">압축을 해제하려면 앱의 프로젝트 파일에 `BlazorEnableCompression` MSBuild 속성을 추가하고 값을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c25d4-127">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="c25d4-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c25d4-128">Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor Server에서 호스트된 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="c25d4-129">다음 두 구성 요소를 사용하는 Blazor WebAssembly 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="c25d4-130">`Main.razor`: 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="c25d4-131">`About.razor`: `About` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="c25d4-132">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="c25d4-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c25d4-133">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-133">The browser makes a request.</span></span>
1. <span data-ttu-id="c25d4-134">기본 페이지(일반적으로 `index.html`)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="c25d4-135">`index.html`이 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-135">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="c25d4-136">Blazor의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-136">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="c25d4-137">Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="c25d4-138">‘Blazor WebAssembly 앱 내’의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c25d4-139">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-139">The router handles the requests internally.</span></span>

<span data-ttu-id="c25d4-140">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c25d4-141">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="c25d4-142">브라우저는 인터넷 기반 호스트에 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 `index.html` 페이지에 다시 써야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="c25d4-143">`index.html`이 반환되는 경우 앱의 Blazor 라우터가 넘겨받아 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="c25d4-144">IIS 서버에 배포하는 경우 앱의 게시된 `web.config` 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="c25d4-145">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c25d4-146">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="c25d4-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c25d4-147">‘호스트된 배포’는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="c25d4-148">클라이언트 Blazor WebAssembly 앱이 서버 앱의 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="c25d4-149">두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-149">The two apps are deployed together.</span></span> <span data-ttu-id="c25d4-150">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c25d4-151">호스트된 배포의 경우 Visual Studio는 **`Hosted`** (`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="c25d4-152">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c25d4-153">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="c25d4-154">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="c25d4-154">Standalone deployment</span></span>

<span data-ttu-id="c25d4-155">‘독립 실행형 배포’는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c25d4-156">모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="c25d4-157">독립 실행형 배포 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c25d4-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c25d4-158">Azure App Service</span></span>

<span data-ttu-id="c25d4-159">Blazor WebAssembly 앱은 [IIS](#iis)에서 앱을 호스트하는 Windows의 Azure App Services에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-159">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="c25d4-160">Linux용 Azure App Service에 독립 실행형 Blazor WebAssembly 앱 배포는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="c25d4-161">지금은 앱을 호스트하는 Linux 서버 이미지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="c25d4-162">이 시나리오를 사용할 수 있도록 하는 작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="c25d4-163">IIS</span><span class="sxs-lookup"><span data-stu-id="c25d4-163">IIS</span></span>

<span data-ttu-id="c25d4-164">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="c25d4-165">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c25d4-166">게시된 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="c25d4-167">웹 서버 또는 호스팅 서비스에서 `publish` 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="c25d4-168">web.config</span><span class="sxs-lookup"><span data-stu-id="c25d4-168">web.config</span></span>

<span data-ttu-id="c25d4-169">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 `web.config` 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c25d4-170">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c25d4-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c25d4-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="c25d4-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c25d4-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="c25d4-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c25d4-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="c25d4-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c25d4-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="c25d4-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c25d4-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="c25d4-176">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c25d4-177">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c25d4-178">앱의 정적 자산이 상주하는 하위 디렉터리(`wwwroot/{PATH REQUESTED}`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="c25d4-179">파일이 아닌 자산 요청이 정적 자산 폴더에 있는 앱의 기본 문서(`wwwroot/index.html`)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="c25d4-180">사용자 지정 web.config 사용</span><span class="sxs-lookup"><span data-stu-id="c25d4-180">Use a custom web.config</span></span>

<span data-ttu-id="c25d4-181">사용자 지정 `web.config` 파일을 사용하려면 사용자 지정 `web.config` 파일을 프로젝트 폴더의 루트에 배치하고 프로젝트를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="c25d4-182">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="c25d4-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="c25d4-183">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c25d4-184">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c25d4-185">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c25d4-186">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c25d4-187">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c25d4-188">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c25d4-189">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c25d4-190">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-190">Copy the installer to the server.</span></span> <span data-ttu-id="c25d4-191">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-191">Run the installer.</span></span> <span data-ttu-id="c25d4-192">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c25d4-193">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="c25d4-194">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="c25d4-194">Configure the website</span></span>

<span data-ttu-id="c25d4-195">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c25d4-196">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-196">The folder contains:</span></span>

* <span data-ttu-id="c25d4-197">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 `web.config` 파일</span><span class="sxs-lookup"><span data-stu-id="c25d4-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c25d4-198">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="c25d4-199">IIS 하위 앱으로 호스트</span><span class="sxs-lookup"><span data-stu-id="c25d4-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="c25d4-200">독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="c25d4-201">상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="c25d4-202">Blazor 앱의 게시된 `web.config` 파일에 `<handlers>` 섹션을 추가하여 파일에서 처리기를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="c25d4-203">`inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="c25d4-204">처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:blazor/host-and-deploy/index#app-base-path)에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="c25d4-205">앱의 `index.html` 파일에 있는 앱 기본 경로를 IIS에서 하위 앱을 구성할 때 사용되는 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="c25d4-206">Brotli 및 Gzip 압축</span><span class="sxs-lookup"><span data-stu-id="c25d4-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="c25d4-207">`web.config`를 통해 IIS를 구성하여 Brotli 또는 Gzip 압축 Blazor 자산을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="c25d4-208">예제 구성은 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true)을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="c25d4-209">문제 해결</span><span class="sxs-lookup"><span data-stu-id="c25d4-209">Troubleshooting</span></span>

<span data-ttu-id="c25d4-210">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c25d4-211">모듈이 설치되지 않은 경우 IIS가 `web.config` 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="c25d4-212">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="c25d4-213">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="c25d4-214">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="c25d4-214">Azure Storage</span></span>

<span data-ttu-id="c25d4-215">[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="c25d4-216">사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="c25d4-217">스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="c25d4-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="c25d4-218">**인덱스 문서 이름**을 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="c25d4-219">**오류 문서 경로**를 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-219">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="c25d4-220">Razor 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob 서비스에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-220">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="c25d4-221">이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="c25d4-222">`index.html` BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="c25d4-223">파일의 `Content-Type` 헤더에 부적절한 MIME 형식으로 인해 런타임에 파일이 로드되지 않을 경우 다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="c25d4-224">파일이 배포될 때 올바른 MIME 형식(`Content-Type` 헤더)을 설정하도록 도구를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="c25d4-225">앱이 배포된 후 파일에 대한 MIME 형식(`Content-Type` 헤더)을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="c25d4-226">각 파일의 스토리지 탐색기(Azure Portal)에서</span><span class="sxs-lookup"><span data-stu-id="c25d4-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="c25d4-227">파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="c25d4-228">**ContentType**을 설정하고 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="c25d4-229">자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="c25d4-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="c25d4-230">Nginx</span></span>

<span data-ttu-id="c25d4-231">다음 `nginx.conf` 파일은 Nginx가 디스크에서 대응하는 파일을 찾을 수 없을 때마다 `index.html` 파일을 보내도록 구성하는 방법을 보여 주기 위해 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="c25d4-232">[`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req)를 사용하여 [NGINX 버스트 속도 한도](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)를 설정하는 경우 Blazor WebAssembly 앱에서 수행하는 비교적 많은 수의 요청을 수용하기 위해 큰 `burst` 매개 변수 값이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-232">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="c25d4-233">처음에는 값을 60 이상으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-233">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="c25d4-234">브라우저 개발자 도구 또는 네트워크 트래픽 도구에서 요청이 ‘503 - 서비스를 사용할 수 없음’ 상태 코드를 수신하는 것으로 확인되는 경우 값을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-234">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="c25d4-235">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-235">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="c25d4-236">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="c25d4-236">Nginx in Docker</span></span>

<span data-ttu-id="c25d4-237">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-237">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c25d4-238">Dockerfile을 업데이트하여 `nginx.config` 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-238">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="c25d4-239">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-239">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="c25d4-240">Apache</span><span class="sxs-lookup"><span data-stu-id="c25d4-240">Apache</span></span>

<span data-ttu-id="c25d4-241">CentOS 7 이상에 Blazor WebAssembly 앱을 배포하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-241">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="c25d4-242">Apache 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-242">Create the Apache configuration file.</span></span> <span data-ttu-id="c25d4-243">다음 예제는 단순화된 구성 파일입니다(`blazorapp.config`).</span><span class="sxs-lookup"><span data-stu-id="c25d4-243">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="c25d4-244">Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-244">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="c25d4-245">앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-245">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="c25d4-246">Apache 서비스를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-246">Restart the Apache service.</span></span>

<span data-ttu-id="c25d4-247">자세한 내용은 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-247">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="c25d4-248">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="c25d4-248">GitHub Pages</span></span>

<span data-ttu-id="c25d4-249">URL 다시 쓰기를 처리하려면 `index.html` 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 `404.html` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-249">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="c25d4-250">커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](https://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-250">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="c25d4-251">커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-251">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="c25d4-252">조직 사이트 대신 프로젝트 사이트를 사용하는 경우 `index.html`의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-252">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="c25d4-253">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="c25d4-253">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c25d4-254">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="c25d4-254">Host configuration values</span></span>

<span data-ttu-id="c25d4-255">[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-255">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c25d4-256">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="c25d4-256">Content root</span></span>

<span data-ttu-id="c25d4-257">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-257">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="c25d4-258">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-258">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="c25d4-259">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c25d4-260">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="c25d4-261">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-261">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c25d4-262">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-262">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="c25d4-263">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c25d4-264">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-264">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="c25d4-265">경로 기준</span><span class="sxs-lookup"><span data-stu-id="c25d4-265">Path base</span></span>

<span data-ttu-id="c25d4-266">`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.)</span><span class="sxs-lookup"><span data-stu-id="c25d4-266">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c25d4-267">다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-267">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="c25d4-268">자세한 내용은 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-268">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c25d4-269">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-269">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c25d4-270">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="c25d4-270">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c25d4-271">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-271">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c25d4-272">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-272">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="c25d4-273">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-273">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c25d4-274">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-274">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="c25d4-275">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-275">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c25d4-276">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-276">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="c25d4-277">URL</span><span class="sxs-lookup"><span data-stu-id="c25d4-277">URLs</span></span>

<span data-ttu-id="c25d4-278">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-278">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c25d4-279">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-279">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c25d4-280">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-280">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c25d4-281">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-281">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c25d4-282">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-282">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c25d4-283">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-283">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c25d4-284">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-284">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="c25d4-285">링커 구성</span><span class="sxs-lookup"><span data-stu-id="c25d4-285">Configure the Linker</span></span>

<span data-ttu-id="c25d4-286">Blazor는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-286">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c25d4-287">자세한 내용은 <xref:blazor/host-and-deploy/configure-linker>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-287">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="c25d4-288">사용자 지정 부팅 리소스 로드</span><span class="sxs-lookup"><span data-stu-id="c25d4-288">Custom boot resource loading</span></span>

<span data-ttu-id="c25d4-289">`loadBootResource` 함수로 Blazor WebAssembly 앱을 초기화하여 기본 제공 부팅 리소스 로드 메커니즘을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-289">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="c25d4-290">다음 시나리오에 `loadBootResource`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-290">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="c25d4-291">사용자가 CDN에서 표준 시간대 데이터 또는 `dotnet.wasm` 같은 정적 리소스를 로드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-291">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="c25d4-292">HTTP 요청을 사용하여 압축된 어셈블리를 로드하고 서버에서 압축된 콘텐츠 페치를 지원하지 않는 호스트의 경우 클라이언트에서 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-292">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="c25d4-293">각 `fetch` 요청을 새 이름으로 리디렉션하여 리소스 별칭을 다른 이름으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-293">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="c25d4-294">`loadBootResource` 매개 변수는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-294">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="c25d4-295">매개 변수</span><span class="sxs-lookup"><span data-stu-id="c25d4-295">Parameter</span></span>    | <span data-ttu-id="c25d4-296">설명</span><span class="sxs-lookup"><span data-stu-id="c25d4-296">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="c25d4-297">리소스 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-297">The type of the resource.</span></span> <span data-ttu-id="c25d4-298">허용되는 형식: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="c25d4-298">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="c25d4-299">리소스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-299">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="c25d4-300">리소스의 상대 또는 절대 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-300">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="c25d4-301">응답에서 예상되는 콘텐츠를 나타내는 무결성 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-301">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="c25d4-302">`loadBootResource`는 로드 프로세스를 재정의하기 위해 다음 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-302">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="c25d4-303">URI 문자열.</span><span class="sxs-lookup"><span data-stu-id="c25d4-303">URI string.</span></span> <span data-ttu-id="c25d4-304">다음 예제(`wwwroot/index.html`)에서 다음 파일은 `https://my-awesome-cdn.com/`의 CDN에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-304">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="c25d4-305">표준 시간대 데이터</span><span class="sxs-lookup"><span data-stu-id="c25d4-305">Timezone data</span></span>

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

* <span data-ttu-id="c25d4-306">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="c25d4-306">`Promise<Response>`.</span></span> <span data-ttu-id="c25d4-307">헤더에서 `integrity` 매개 변수를 전달하여 기본 무결성 검사 동작을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-307">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="c25d4-308">다음 예제(`wwwroot/index.html`)에서는 사용자 지정 HTTP 헤더를 아웃바운드 요청에 추가하고 `integrity` 매개 변수를 `fetch` 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-308">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="c25d4-309">`null`/`undefined`, 기본 로드 동작이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-309">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="c25d4-310">외부 소스는 브라우저에서 원본 간 리소스 로드를 허용하는 데 필요한 CORS 헤더를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-310">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="c25d4-311">일반적으로 CDN은 필요한 헤더를 기본적으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-311">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="c25d4-312">사용자 지정 동작의 경우에만 형식을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-312">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="c25d4-313">`loadBootResource`에 지정되지 않은 형식은 기본 로드 동작에 따라 프레임워크에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-313">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="c25d4-314">DLL 파일의 파일 이름 확장명 변경</span><span class="sxs-lookup"><span data-stu-id="c25d4-314">Change the filename extension of DLL files</span></span>

<span data-ttu-id="c25d4-315">앱 게시 `.dll` 파일의 파일 이름 확장명을 변경해야 하는 경우 이 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-315">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="c25d4-316">앱을 게시한 후 셸 스크립트 또는 DevOps 빌드 파이프라인을 사용하여 다른 파일 확장명을 사용하도록 `.dll` 파일의 이름을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-316">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="c25d4-317">앱 게시 출력(예: `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`)의 `wwwroot` 디렉터리에 있는 `.dll` 파일을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-317">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="c25d4-318">다음 예제에서는 `.bin` 파일 확장명을 사용하도록 `.dll` 파일의 이름이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-318">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="c25d4-319">Windows에서:</span><span class="sxs-lookup"><span data-stu-id="c25d4-319">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="c25d4-320">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-320">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="c25d4-321">Linux 또는 macOS에서:</span><span class="sxs-lookup"><span data-stu-id="c25d4-321">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="c25d4-322">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-322">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="c25d4-323">`.bin` 이외의 다른 파일 확장명을 사용하려면 이전 명령에서 `.bin`을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-323">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="c25d4-324">압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 처리하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-324">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="c25d4-325">압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-325">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="c25d4-326">압축은 이 접근 방법으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-326">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="c25d4-327">업데이트된 `blazor.boot.json` 파일을 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-327">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="c25d4-328">위의 지침은 서비스 작업자 자산을 사용 중인 경우에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-328">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="c25d4-329">`wwwroot/service-worker-assets.js.br` 및 `wwwroot/service-worker-assets.js.gz`를 제거하거나 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-329">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="c25d4-330">그렇지 않으면 브라우저에서 파일 무결성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-330">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="c25d4-331">다음 Windows 예제에서는 프로젝트의 루트에 배치된 PowerShell 스크립트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-331">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="c25d4-332">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="c25d4-332">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="c25d4-333">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-333">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="c25d4-334">프로젝트 파일에서 스크립트는 앱을 게시한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c25d4-334">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="c25d4-335">피드백을 제공하려면 [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477)을 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="c25d4-335">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
