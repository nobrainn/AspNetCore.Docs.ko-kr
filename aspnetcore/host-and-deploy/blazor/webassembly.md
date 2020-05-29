---
<span data-ttu-id="ff0cf-101">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-103">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-104">'Identity'</span></span>
- <span data-ttu-id="ff0cf-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-106">'Razor'</span></span>
- <span data-ttu-id="ff0cf-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="ff0cf-108">ASP.NET Core Blazor WebAssembly 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="ff0cf-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ff0cf-109">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) 및 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="ff0cf-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="ff0cf-110">[Blazor WebAssembly 호스팅 모델 사용](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ff0cf-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ff0cf-111">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 병렬로 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="ff0cf-112">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ff0cf-113">다음 배포 전략이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ff0cf-114">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ff0cf-115">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ff0cf-116">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ff0cf-117">이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="ff0cf-118">Brotli 사전 압축</span><span class="sxs-lookup"><span data-stu-id="ff0cf-118">Brotli precompression</span></span>

<span data-ttu-id="ff0cf-119">Blazor WebAssembly 앱이 게시될 때 [Brotli 압축 알고리즘](https://tools.ietf.org/html/rfc7932)을 사용해 출력을 사전 압축하여 앱 크기를 줄이고 런타임 압축이 필요 없게 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="ff0cf-120">IIS *web.config* 압축 구성에 대해서는 [IIS: Brotli 및 Gzip 압축](#brotli-and-gzip-compression) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ff0cf-121">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="ff0cf-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ff0cf-122">Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor 서버에서 호스트한 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ff0cf-123">다음 두 가지 구성 요소가 있는 Blazor WebAssembly 앱을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ff0cf-124">*Main.razor* &ndash; 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-124">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ff0cf-125">*About.razor* &ndash; `About` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-125">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="ff0cf-126">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="ff0cf-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ff0cf-127">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-127">The browser makes a request.</span></span>
1. <span data-ttu-id="ff0cf-128">기본 페이지(일반적으로 *index.html*)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ff0cf-129">*index.html* 앱을 부트스트랩으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="ff0cf-130">의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ff0cf-131">Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ff0cf-132">*Blazor WebAssembly 앱 내*의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ff0cf-133">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-133">The router handles the requests internally.</span></span>

<span data-ttu-id="ff0cf-134">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ff0cf-135">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ff0cf-136">브라우저는 인터넷 기반 호스트에 대해 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 *index.html* 페이지에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ff0cf-137">*index.html*이 반환되는 경우 앱의 Blazor 라우터가 인수하여 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ff0cf-138">IIS 서버에 배포하는 경우 앱의 게시된 *web.config* 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ff0cf-139">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ff0cf-140">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="ff0cf-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ff0cf-141">*호스트된 배포*는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ff0cf-142">클라이언트 Blazor WebAssembly 앱이 서버 앱의 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ff0cf-143">두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-143">The two apps are deployed together.</span></span> <span data-ttu-id="ff0cf-144">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ff0cf-145">호스트된 배포의 경우 Visual Studio에는 **Hosted**(`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ff0cf-146">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ff0cf-147">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ff0cf-148">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="ff0cf-148">Standalone deployment</span></span>

<span data-ttu-id="ff0cf-149">*독립 실행형 배포*는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ff0cf-150">모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ff0cf-151">독립 실행형 배포 자산은 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="ff0cf-152">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ff0cf-152">Azure App Service</span></span>

Blazor<span data-ttu-id="ff0cf-153"> WebAssembly 앱은 [IIS](#iis)에서 앱을 호스트하는 Windows의 Azure App Services에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-153"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="ff0cf-154">Linux용 Azure App Service에 독립 실행형 Blazor WebAssembly 앱 배포는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="ff0cf-155">지금은 앱을 호스트하는 Linux 서버 이미지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="ff0cf-156">이 시나리오를 사용할 수 있도록 하는 작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="ff0cf-157">IIS</span><span class="sxs-lookup"><span data-stu-id="ff0cf-157">IIS</span></span>

<span data-ttu-id="ff0cf-158">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ff0cf-159">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ff0cf-160">게시된 자산은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ff0cf-161">웹 서버 또는 호스팅 서비스에서 *publish* 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ff0cf-162">web.config</span><span class="sxs-lookup"><span data-stu-id="ff0cf-162">web.config</span></span>

<span data-ttu-id="ff0cf-163">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 *web.config* 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ff0cf-164">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ff0cf-165">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="ff0cf-165">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="ff0cf-166">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="ff0cf-166">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="ff0cf-167">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="ff0cf-167">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="ff0cf-168">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ff0cf-168">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="ff0cf-169">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ff0cf-169">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="ff0cf-170">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ff0cf-171">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ff0cf-172">앱의 정적 자산이 상주하는 하위 디렉터리(*wwwroot/{PATH REQUESTED}* )를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ff0cf-173">파일이 아닌 자산에 대한 요청이 정적 자산 폴더에 있는 앱의 기본 문서(*wwwroot/index.html*)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="ff0cf-174">사용자 지정 web.config 사용</span><span class="sxs-lookup"><span data-stu-id="ff0cf-174">Use a custom web.config</span></span>

<span data-ttu-id="ff0cf-175">사용자 지정 *web.config* 파일을 사용하려면 사용자 지정 *web.config* 파일을 프로젝트 폴더의 루트에 배치하고 프로젝트를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ff0cf-176">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="ff0cf-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ff0cf-177">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ff0cf-178">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ff0cf-179">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ff0cf-180">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ff0cf-181">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ff0cf-182">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ff0cf-183">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ff0cf-184">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-184">Copy the installer to the server.</span></span> <span data-ttu-id="ff0cf-185">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-185">Run the installer.</span></span> <span data-ttu-id="ff0cf-186">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ff0cf-187">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ff0cf-188">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="ff0cf-188">Configure the website</span></span>

<span data-ttu-id="ff0cf-189">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ff0cf-190">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-190">The folder contains:</span></span>

* <span data-ttu-id="ff0cf-191">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 *web.config* 파일.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ff0cf-192">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ff0cf-193">IIS 하위 앱으로 호스트</span><span class="sxs-lookup"><span data-stu-id="ff0cf-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="ff0cf-194">독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ff0cf-195">상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ff0cf-196">Blazor 앱의 게시된 *web.config* 파일에 `<handlers>` 섹션을 추가하여 파일에서 핸들러를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ff0cf-197">`inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="ff0cf-198">처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:host-and-deploy/blazor/index#app-base-path)에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ff0cf-199">IIS에서 하위 앱을 구성할 때
앱의 *index.html* 파일에서 앱 기본 경로를 사용한 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="ff0cf-200">Brotli 및 Gzip 압축</span><span class="sxs-lookup"><span data-stu-id="ff0cf-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="ff0cf-201">*web.config*를 통해 IIS를 구성하여 Brotli 또는 Gzip 압축 Blazor 자산을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="ff0cf-202">예제 구성은 [web.config](webassembly/_samples/web.config?raw=true)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ff0cf-203">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ff0cf-203">Troubleshooting</span></span>

<span data-ttu-id="ff0cf-204">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ff0cf-205">모듈이 설치되지 않은 경우 IIS가 *web.config* 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ff0cf-206">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ff0cf-207">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ff0cf-208">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="ff0cf-208">Azure Storage</span></span>

<span data-ttu-id="ff0cf-209">[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ff0cf-210">사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ff0cf-211">스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ff0cf-212">**인덱스 문서 이름**을 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ff0cf-213">**오류 문서 경로**를 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="ff0cf-214"> 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob 서비스에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ff0cf-215">이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ff0cf-216">*index.html* BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ff0cf-217">자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ff0cf-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="ff0cf-218">Nginx</span></span>

<span data-ttu-id="ff0cf-219">다음 *nginx.conf* 파일은 Nginx가 디스크에서 *index.html* 파일을 찾을 수 없을 때마다 해당 파일을 보내도록 구성하는 방법을 보여 주도록 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="ff0cf-220">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ff0cf-221">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="ff0cf-221">Nginx in Docker</span></span>

<span data-ttu-id="ff0cf-222">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ff0cf-223">Dockerfile을 업데이트하여 *nginx.config* 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ff0cf-224">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ff0cf-225">Apache</span><span class="sxs-lookup"><span data-stu-id="ff0cf-225">Apache</span></span>

<span data-ttu-id="ff0cf-226">Blazor WebAssembly 앱을 CentOS 7 이상에 배포하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ff0cf-227">Apache 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-227">Create the Apache configuration file.</span></span> <span data-ttu-id="ff0cf-228">다음 예제는 단순화된 구성 파일입니다(*blazorapp.config*).</span><span class="sxs-lookup"><span data-stu-id="ff0cf-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="ff0cf-229">Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ff0cf-230">앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ff0cf-231">Apache 서비스를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-231">Restart the Apache service.</span></span>

<span data-ttu-id="ff0cf-232">자세한 내용은 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ff0cf-233">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="ff0cf-233">GitHub Pages</span></span>

<span data-ttu-id="ff0cf-234">URL 다시 생성을 처리하려면 *index.html* 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 *404.html* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ff0cf-235">커뮤니티에서 제공하는 예제 구현은 [GitHub 페이지에 대한 단일 페이지 앱](https://spa-github-pages.rafrex.com/)([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme))을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ff0cf-236">커뮤니티 방식을 사용하는 예는 [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io)([실시간 사이트](https://blazor-demo.github.io/))를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ff0cf-237">조직의 사이트 대신 프로젝트 사이트를 사용하는 경우 *index.html*의 `<base>` 태그를 추가하거나 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ff0cf-238">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="ff0cf-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ff0cf-239">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="ff0cf-239">Host configuration values</span></span>

<span data-ttu-id="ff0cf-240">[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ff0cf-241">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="ff0cf-241">Content root</span></span>

<span data-ttu-id="ff0cf-242">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ff0cf-243">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ff0cf-244">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff0cf-245">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ff0cf-246">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff0cf-247">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ff0cf-248">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff0cf-249">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ff0cf-250">경로 기준</span><span class="sxs-lookup"><span data-stu-id="ff0cf-250">Path base</span></span>

<span data-ttu-id="ff0cf-251">`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.)</span><span class="sxs-lookup"><span data-stu-id="ff0cf-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ff0cf-252">다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ff0cf-253">자세한 내용은 [앱 기본 경로](xref:host-and-deploy/blazor/index#app-base-path)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff0cf-254">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ff0cf-255">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="ff0cf-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ff0cf-256">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff0cf-257">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ff0cf-258">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff0cf-259">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ff0cf-260">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff0cf-261">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ff0cf-262">URL</span><span class="sxs-lookup"><span data-stu-id="ff0cf-262">URLs</span></span>

<span data-ttu-id="ff0cf-263">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ff0cf-264">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ff0cf-265">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ff0cf-266">**IIS Express** 프로필의 *launchSettings.json* 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ff0cf-267">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ff0cf-268">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ff0cf-269">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수를 *launchSettings.json* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ff0cf-270">링커 구성</span><span class="sxs-lookup"><span data-stu-id="ff0cf-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="ff0cf-271">는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ff0cf-272">자세한 내용은 <xref:host-and-deploy/blazor/configure-linker>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="ff0cf-273">사용자 지정 부팅 리소스 로드</span><span class="sxs-lookup"><span data-stu-id="ff0cf-273">Custom boot resource loading</span></span>

<span data-ttu-id="ff0cf-274">`loadBootResource` 함수로 Blazor WebAssembly 앱을 초기화하여 기본 제공 부팅 리소스 로드 메커니즘을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="ff0cf-275">다음 시나리오에 `loadBootResource`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="ff0cf-276">사용자가 CDN에서 표준 시간대 데이터 또는 *dotnet.wasm* 같은 정적 리소스를 로드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="ff0cf-277">HTTP 요청을 사용하여 압축된 어셈블리를 로드하고 서버에서 압축된 콘텐츠 페치를 지원하지 않는 호스트의 경우 클라이언트에서 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="ff0cf-278">각 `fetch` 요청을 새 이름으로 리디렉션하여 리소스 별칭을 다른 이름으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="ff0cf-279">`loadBootResource` 매개 변수는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="ff0cf-280">매개 변수</span><span class="sxs-lookup"><span data-stu-id="ff0cf-280">Parameter</span></span>    | <span data-ttu-id="ff0cf-281">설명</span><span class="sxs-lookup"><span data-stu-id="ff0cf-281">Description</span></span> |
| ---
<span data-ttu-id="ff0cf-282">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-284">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-285">'Identity'</span></span>
- <span data-ttu-id="ff0cf-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-287">'Razor'</span></span>
- <span data-ttu-id="ff0cf-288">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ff0cf-289">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-291">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-292">'Identity'</span></span>
- <span data-ttu-id="ff0cf-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-294">'Razor'</span></span>
- <span data-ttu-id="ff0cf-295">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ff0cf-296">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-298">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-299">'Identity'</span></span>
- <span data-ttu-id="ff0cf-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-301">'Razor'</span></span>
- <span data-ttu-id="ff0cf-302">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ff0cf-303">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-305">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-306">'Identity'</span></span>
- <span data-ttu-id="ff0cf-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-308">'Razor'</span></span>
- <span data-ttu-id="ff0cf-309">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-309">'SignalR' uid:</span></span> 

<span data-ttu-id="ff0cf-310">------ | --- title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-312">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-312">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-313">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-313">'Identity'</span></span>
- <span data-ttu-id="ff0cf-314">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-315">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-315">'Razor'</span></span>
- <span data-ttu-id="ff0cf-316">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ff0cf-317">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-319">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-320">'Identity'</span></span>
- <span data-ttu-id="ff0cf-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-322">'Razor'</span></span>
- <span data-ttu-id="ff0cf-323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ff0cf-324">title: 'ASP.NET Core Blazor WebAssembly 호스트 및 배포' author: description: 'ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ff0cf-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ff0cf-326">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-326">'Blazor'</span></span>
- <span data-ttu-id="ff0cf-327">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-327">'Identity'</span></span>
- <span data-ttu-id="ff0cf-328">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="ff0cf-329">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ff0cf-329">'Razor'</span></span>
- <span data-ttu-id="ff0cf-330">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-330">'SignalR' uid:</span></span> 

<span data-ttu-id="ff0cf-331">------ | | `type`       | 리소스의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="ff0cf-332">허용되는 형식: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | 리소스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="ff0cf-333">| | `defaultUri` | 리소스의 상대 또는 절대 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="ff0cf-334">| | `integrity`  | 응답에서 예상되는 콘텐츠를 나타내는 무결성 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="ff0cf-335">`loadBootResource`는 로드 프로세스를 재정의하기 위해 다음 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="ff0cf-336">URI 문자열.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-336">URI string.</span></span> <span data-ttu-id="ff0cf-337">다음 예제(*wwwroot/index.html*)에서 다음 파일은 `https://my-awesome-cdn.com/`의 CDN에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="ff0cf-338">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="ff0cf-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="ff0cf-339">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="ff0cf-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="ff0cf-340">표준 시간대 데이터</span><span class="sxs-lookup"><span data-stu-id="ff0cf-340">Timezone data</span></span>

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

* <span data-ttu-id="ff0cf-341">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-341">`Promise<Response>`.</span></span> <span data-ttu-id="ff0cf-342">헤더에서 `integrity` 매개 변수를 전달하여 기본 무결성 검사 동작을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="ff0cf-343">다음 예제(*wwwroot/index.html*)에서는 사용자 지정 HTTP 헤더를 아웃바운드 요청에 추가하고 `integrity` 매개 변수를 `fetch` 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="ff0cf-344">`null`/`undefined`, 기본 로드 동작이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="ff0cf-345">외부 소스는 브라우저에서 원본 간 리소스 로드를 허용하는 데 필요한 CORS 헤더를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="ff0cf-346">일반적으로 CDN은 필요한 헤더를 기본적으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="ff0cf-347">사용자 지정 동작의 경우에만 형식을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="ff0cf-348">`loadBootResource`에 지정되지 않은 형식은 기본 로드 동작에 따라 프레임워크에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="ff0cf-349">DLL 파일의 파일 이름 확장명 변경</span><span class="sxs-lookup"><span data-stu-id="ff0cf-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="ff0cf-350">앱 게시 *.dll* 파일의 파일 이름 확장명을 변경해야 하는 경우 이 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="ff0cf-351">앱을 게시한 후 셸 스크립트 또는 DevOps 빌드 파이프라인을 사용하여 다른 파일 확장명을 사용하도록 *.dll* 파일의 이름을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="ff0cf-352">앱 게시 출력(예: *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*)의 *wwwroot* 디렉터리에 있는 *.dll* 파일을 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="ff0cf-353">다음 예제에서는 *.bin* 파일 확장명을 사용하도록 *.dll* 파일의 이름이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="ff0cf-354">Windows에서:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="ff0cf-355">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="ff0cf-356">Linux 또는 macOS에서:</span><span class="sxs-lookup"><span data-stu-id="ff0cf-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="ff0cf-357">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="ff0cf-358">*.bin* 이외의 다른 파일 확장명을 사용하려면 이전 명령에서 *.bin*을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="ff0cf-359">압축된 *blazor.boot.json.gz* 및 *blazor.boot.json.br* 파일을 처리하려면 다음 접근 방법 중 하나를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="ff0cf-360">압축된 *blazor.boot.json.gz* 및 *blazor.boot.json.br* 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="ff0cf-361">압축은 이 접근 방법으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="ff0cf-362">업데이트된 *blazor.boot.json* 파일을 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="ff0cf-363">위의 지침은 서비스 작업자 자산을 사용 중인 경우에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="ff0cf-364">*wwwroot/service-worker-assets.js.br* 및 *wwwroot/service-worker-assets.js.gz*를 제거하거나 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="ff0cf-365">그렇지 않으면 브라우저에서 파일 무결성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="ff0cf-366">다음 Windows 예제에서는 프로젝트의 루트에 배치된 PowerShell 스크립트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="ff0cf-367">*ChangeDLLExtensions.ps1:* :</span><span class="sxs-lookup"><span data-stu-id="ff0cf-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="ff0cf-368">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="ff0cf-369">프로젝트 파일에서 스크립트는 앱을 게시한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="ff0cf-370">피드백을 제공하려면 [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477)을 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="ff0cf-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 