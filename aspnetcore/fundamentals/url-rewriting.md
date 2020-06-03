---
<span data-ttu-id="1bbfc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-102">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-103">'Identity'</span></span>
- <span data-ttu-id="1bbfc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-105">'Razor'</span></span>
- <span data-ttu-id="1bbfc-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="1bbfc-107">ASP.NET Core에서 URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1bbfc-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="1bbfc-108">작성자: [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bbfc-109">본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1bbfc-110">URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1bbfc-111">URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1bbfc-112">URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1bbfc-113">서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1bbfc-114">여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1bbfc-115">들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1bbfc-116">SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1bbfc-117">친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1bbfc-118">안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1bbfc-119">외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-120">URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1bbfc-121">가능한 경우 규칙의 수와 복잡성을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1bbfc-122">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1bbfc-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1bbfc-123">URL 리디렉션 및 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1bbfc-124">*URL 리디렉션*과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1bbfc-125">ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1bbfc-126">*URL 리디렉션*은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1bbfc-127">이 경우 서버를 왕복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-127">This requires a round trip to the server.</span></span> <span data-ttu-id="1bbfc-128">클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1bbfc-129">`/resource`가 `/different-resource`로 *리디렉션*되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1bbfc-135">요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1bbfc-136">*301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1bbfc-137">*301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="1bbfc-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1bbfc-138">*302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1bbfc-139">302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1bbfc-140">상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1bbfc-141">*URL 재작성*은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1bbfc-142">URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1bbfc-143">다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1bbfc-144">`/resource`가 `/different-resource`에 *다시 작성*되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1bbfc-145">클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1bbfc-150">URL 재작성 예제 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="1bbfc-150">URL rewriting sample app</span></span>

<span data-ttu-id="1bbfc-151">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1bbfc-152">이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1bbfc-153">URL 재작성 미들웨어를 사용해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="1bbfc-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1bbfc-154">다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* <span data-ttu-id="1bbfc-155">Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-155">[URL Rewrite module with IIS on Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)</span></span>
* <span data-ttu-id="1bbfc-156">Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-156">[Apache mod_rewrite module on Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* [<span data-ttu-id="1bbfc-157">Nginx의 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1bbfc-158">또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1bbfc-159">IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1bbfc-160">미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1bbfc-161">서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1bbfc-162">바로 이런 시나리오에서 대신 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1bbfc-163">미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1bbfc-164">벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1bbfc-165">패키지</span><span class="sxs-lookup"><span data-stu-id="1bbfc-165">Package</span></span>

<span data-ttu-id="1bbfc-166">URL 다시 작성 미들웨어는 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1bbfc-167">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-167">Extension and options</span></span>

<span data-ttu-id="1bbfc-168">각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1bbfc-169">처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1bbfc-170">`RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1bbfc-171">www 이외 요청을 www로 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-171">Redirect non-www to www</span></span>

<span data-ttu-id="1bbfc-172">이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1bbfc-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1bbfc-174">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1bbfc-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1bbfc-176">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1bbfc-177">오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1bbfc-178">상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1bbfc-179">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-179">URL redirect</span></span>

<span data-ttu-id="1bbfc-180">요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1bbfc-181">첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1bbfc-182">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="1bbfc-183">필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1bbfc-184">상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음*으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1bbfc-185">개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1bbfc-186">그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1bbfc-187">리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1bbfc-188">브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1bbfc-189">샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1bbfc-190">두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1bbfc-191">응답 본문에 리디렉션 URL이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1bbfc-192">URL이 *리디렉션*되면 서버로의 왕복이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1bbfc-193">리디렉션 규칙을 설정할 때에는 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1bbfc-194">리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1bbfc-195">따라서 *무한 리디렉션 루프*를 실수로 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1bbfc-196">원본 요청: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1bbfc-198">표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1bbfc-199">그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1bbfc-200">마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1bbfc-201">따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1bbfc-202">즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1bbfc-203">대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1bbfc-204">첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1bbfc-205">예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1bbfc-206">규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1bbfc-207">보안 엔드포인트에 대한 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1bbfc-208"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1bbfc-209">상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1bbfc-210">포트가 제공되지 않는 경우 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="1bbfc-211">미들웨어가 기본적으로 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1bbfc-212">체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1bbfc-213">다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨*으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1bbfc-214"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1bbfc-215">미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨*으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1bbfc-216">추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1bbfc-217">자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1bbfc-218">예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1bbfc-219">먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1bbfc-220">모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1bbfc-221">자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1bbfc-222">`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1bbfc-224">`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1bbfc-226">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-226">URL rewrite</span></span>

<span data-ttu-id="1bbfc-227">URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1bbfc-228">첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1bbfc-229">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="1bbfc-230">세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1bbfc-231">원래 요청: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1bbfc-233">식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1bbfc-234">`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1bbfc-235">따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1bbfc-236">경로</span><span class="sxs-lookup"><span data-stu-id="1bbfc-236">Path</span></span>                               | <span data-ttu-id="1bbfc-237">일치</span><span class="sxs-lookup"><span data-stu-id="1bbfc-237">Match</span></span> |
| ---
<span data-ttu-id="1bbfc-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-239">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-240">'Identity'</span></span>
- <span data-ttu-id="1bbfc-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-242">'Razor'</span></span>
- <span data-ttu-id="1bbfc-243">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-245">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-246">'Identity'</span></span>
- <span data-ttu-id="1bbfc-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-248">'Razor'</span></span>
- <span data-ttu-id="1bbfc-249">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-251">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-252">'Identity'</span></span>
- <span data-ttu-id="1bbfc-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-254">'Razor'</span></span>
- <span data-ttu-id="1bbfc-255">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-257">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-258">'Identity'</span></span>
- <span data-ttu-id="1bbfc-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-260">'Razor'</span></span>
- <span data-ttu-id="1bbfc-261">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-263">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-264">'Identity'</span></span>
- <span data-ttu-id="1bbfc-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-266">'Razor'</span></span>
- <span data-ttu-id="1bbfc-267">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-269">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-270">'Identity'</span></span>
- <span data-ttu-id="1bbfc-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-272">'Razor'</span></span>
- <span data-ttu-id="1bbfc-273">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-275">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-276">'Identity'</span></span>
- <span data-ttu-id="1bbfc-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-278">'Razor'</span></span>
- <span data-ttu-id="1bbfc-279">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-281">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-282">'Identity'</span></span>
- <span data-ttu-id="1bbfc-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-284">'Razor'</span></span>
- <span data-ttu-id="1bbfc-285">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-287">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-288">'Identity'</span></span>
- <span data-ttu-id="1bbfc-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-290">'Razor'</span></span>
- <span data-ttu-id="1bbfc-291">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-293">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-294">'Identity'</span></span>
- <span data-ttu-id="1bbfc-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-296">'Razor'</span></span>
- <span data-ttu-id="1bbfc-297">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-299">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-300">'Identity'</span></span>
- <span data-ttu-id="1bbfc-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-302">'Razor'</span></span>
- <span data-ttu-id="1bbfc-303">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-305">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-306">'Identity'</span></span>
- <span data-ttu-id="1bbfc-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-308">'Razor'</span></span>
- <span data-ttu-id="1bbfc-309">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-311">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-312">'Identity'</span></span>
- <span data-ttu-id="1bbfc-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-314">'Razor'</span></span>
- <span data-ttu-id="1bbfc-315">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-317">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-318">'Identity'</span></span>
- <span data-ttu-id="1bbfc-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-320">'Razor'</span></span>
- <span data-ttu-id="1bbfc-321">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-323">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-324">'Identity'</span></span>
- <span data-ttu-id="1bbfc-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-326">'Razor'</span></span>
- <span data-ttu-id="1bbfc-327">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-327">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | 예   | | `/my-cool-redirect-rule/1234/5678` | 예   | | `/anotherredirect-rule/1234/5678`  | 예   |</span><span class="sxs-lookup"><span data-stu-id="1bbfc-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="1bbfc-329">반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1bbfc-330">다음 표에는 일치에서의 차이가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1bbfc-331">경로</span><span class="sxs-lookup"><span data-stu-id="1bbfc-331">Path</span></span>                              | <span data-ttu-id="1bbfc-332">일치</span><span class="sxs-lookup"><span data-stu-id="1bbfc-332">Match</span></span> |
| ---
<span data-ttu-id="1bbfc-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-334">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-335">'Identity'</span></span>
- <span data-ttu-id="1bbfc-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-337">'Razor'</span></span>
- <span data-ttu-id="1bbfc-338">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-340">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-341">'Identity'</span></span>
- <span data-ttu-id="1bbfc-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-343">'Razor'</span></span>
- <span data-ttu-id="1bbfc-344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-346">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-347">'Identity'</span></span>
- <span data-ttu-id="1bbfc-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-349">'Razor'</span></span>
- <span data-ttu-id="1bbfc-350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-352">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-353">'Identity'</span></span>
- <span data-ttu-id="1bbfc-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-355">'Razor'</span></span>
- <span data-ttu-id="1bbfc-356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-358">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-359">'Identity'</span></span>
- <span data-ttu-id="1bbfc-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-361">'Razor'</span></span>
- <span data-ttu-id="1bbfc-362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-364">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-365">'Identity'</span></span>
- <span data-ttu-id="1bbfc-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-367">'Razor'</span></span>
- <span data-ttu-id="1bbfc-368">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-370">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-371">'Identity'</span></span>
- <span data-ttu-id="1bbfc-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-373">'Razor'</span></span>
- <span data-ttu-id="1bbfc-374">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-376">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-377">'Identity'</span></span>
- <span data-ttu-id="1bbfc-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-379">'Razor'</span></span>
- <span data-ttu-id="1bbfc-380">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-382">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-383">'Identity'</span></span>
- <span data-ttu-id="1bbfc-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-385">'Razor'</span></span>
- <span data-ttu-id="1bbfc-386">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-388">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-389">'Identity'</span></span>
- <span data-ttu-id="1bbfc-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-391">'Razor'</span></span>
- <span data-ttu-id="1bbfc-392">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-394">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-395">'Identity'</span></span>
- <span data-ttu-id="1bbfc-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-397">'Razor'</span></span>
- <span data-ttu-id="1bbfc-398">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-400">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-401">'Identity'</span></span>
- <span data-ttu-id="1bbfc-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-403">'Razor'</span></span>
- <span data-ttu-id="1bbfc-404">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-406">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-407">'Identity'</span></span>
- <span data-ttu-id="1bbfc-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-409">'Razor'</span></span>
- <span data-ttu-id="1bbfc-410">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-412">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-413">'Identity'</span></span>
- <span data-ttu-id="1bbfc-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-415">'Razor'</span></span>
- <span data-ttu-id="1bbfc-416">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-416">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | 예   | | `/my-cool-rewrite-rule/1234/5678` | 아니요    | | `/anotherrewrite-rule/1234/5678`  | 아니요    |</span><span class="sxs-lookup"><span data-stu-id="1bbfc-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="1bbfc-418">표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1bbfc-419">여기서 `\d`는 *숫자 하나와 일치*함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1bbfc-420">그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치*함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1bbfc-421">따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1bbfc-422">이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1bbfc-423">재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1bbfc-424">즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1bbfc-425">원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1bbfc-426">리소스를 가져오기 위해 서버를 왕복하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1bbfc-427">리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1bbfc-428">클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1bbfc-429">클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-430">일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1bbfc-431">최대한 빠른 응용 프로그램 응답을 위해서는:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1bbfc-432">재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1bbfc-433">일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1bbfc-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1bbfc-434">Apache mod_rewrite</span></span>

<span data-ttu-id="1bbfc-435"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1bbfc-436">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1bbfc-437">mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1bbfc-438"><xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1bbfc-439">예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1bbfc-440">응답 상태 코드는 *302 - 있음*입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1bbfc-441">원본 요청: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1bbfc-443">미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1bbfc-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1bbfc-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1bbfc-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1bbfc-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="1bbfc-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1bbfc-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1bbfc-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1bbfc-449">HTTP_HOST</span></span>
* <span data-ttu-id="1bbfc-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1bbfc-450">HTTP_REFERER</span></span>
* <span data-ttu-id="1bbfc-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1bbfc-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1bbfc-452">HTTPS</span></span>
* <span data-ttu-id="1bbfc-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="1bbfc-453">IPV6</span></span>
* <span data-ttu-id="1bbfc-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1bbfc-454">QUERY_STRING</span></span>
* <span data-ttu-id="1bbfc-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-456">REMOTE_PORT</span></span>
* <span data-ttu-id="1bbfc-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1bbfc-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1bbfc-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="1bbfc-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1bbfc-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1bbfc-460">REQUEST_URI</span></span>
* <span data-ttu-id="1bbfc-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1bbfc-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-462">SERVER_ADDR</span></span>
* <span data-ttu-id="1bbfc-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-463">SERVER_PORT</span></span>
* <span data-ttu-id="1bbfc-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1bbfc-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1bbfc-465">TIME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-465">TIME</span></span>
* <span data-ttu-id="1bbfc-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1bbfc-466">TIME_DAY</span></span>
* <span data-ttu-id="1bbfc-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-467">TIME_HOUR</span></span>
* <span data-ttu-id="1bbfc-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1bbfc-468">TIME_MIN</span></span>
* <span data-ttu-id="1bbfc-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1bbfc-469">TIME_MON</span></span>
* <span data-ttu-id="1bbfc-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1bbfc-470">TIME_SEC</span></span>
* <span data-ttu-id="1bbfc-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1bbfc-471">TIME_WDAY</span></span>
* <span data-ttu-id="1bbfc-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1bbfc-473">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1bbfc-474">IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1bbfc-475">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1bbfc-476">Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1bbfc-477">IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1bbfc-478">IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1bbfc-479"><xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1bbfc-480">예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1bbfc-481">응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1bbfc-482">원본 요청: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1bbfc-484">원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1bbfc-485">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1bbfc-486">지원되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="1bbfc-486">Unsupported features</span></span>

<span data-ttu-id="1bbfc-487">ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1bbfc-488">아웃바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-488">Outbound Rules</span></span>
* <span data-ttu-id="1bbfc-489">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="1bbfc-489">Custom Server Variables</span></span>
* <span data-ttu-id="1bbfc-490">와일드카드</span><span class="sxs-lookup"><span data-stu-id="1bbfc-490">Wildcards</span></span>
* <span data-ttu-id="1bbfc-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="1bbfc-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1bbfc-492">지원되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="1bbfc-492">Supported server variables</span></span>

<span data-ttu-id="1bbfc-493">미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1bbfc-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1bbfc-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1bbfc-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="1bbfc-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1bbfc-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1bbfc-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1bbfc-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="1bbfc-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1bbfc-499">HTTP_HOST</span></span>
* <span data-ttu-id="1bbfc-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1bbfc-500">HTTP_REFERER</span></span>
* <span data-ttu-id="1bbfc-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1bbfc-501">HTTP_URL</span></span>
* <span data-ttu-id="1bbfc-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1bbfc-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1bbfc-503">HTTPS</span></span>
* <span data-ttu-id="1bbfc-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="1bbfc-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1bbfc-505">QUERY_STRING</span></span>
* <span data-ttu-id="1bbfc-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-507">REMOTE_PORT</span></span>
* <span data-ttu-id="1bbfc-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1bbfc-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1bbfc-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-510"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1bbfc-511">이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1bbfc-512">재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1bbfc-513">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-513">Method-based rule</span></span>

<span data-ttu-id="1bbfc-514">메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1bbfc-515">`Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1bbfc-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1bbfc-517">값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="1bbfc-518">작업</span><span class="sxs-lookup"><span data-stu-id="1bbfc-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="1bbfc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-520">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-521">'Identity'</span></span>
- <span data-ttu-id="1bbfc-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-523">'Razor'</span></span>
- <span data-ttu-id="1bbfc-524">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-526">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-527">'Identity'</span></span>
- <span data-ttu-id="1bbfc-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-529">'Razor'</span></span>
- <span data-ttu-id="1bbfc-530">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-532">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-533">'Identity'</span></span>
- <span data-ttu-id="1bbfc-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-535">'Razor'</span></span>
- <span data-ttu-id="1bbfc-536">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-538">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-539">'Identity'</span></span>
- <span data-ttu-id="1bbfc-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-541">'Razor'</span></span>
- <span data-ttu-id="1bbfc-542">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-544">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-545">'Identity'</span></span>
- <span data-ttu-id="1bbfc-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-547">'Razor'</span></span>
- <span data-ttu-id="1bbfc-548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-550">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-551">'Identity'</span></span>
- <span data-ttu-id="1bbfc-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-553">'Razor'</span></span>
- <span data-ttu-id="1bbfc-554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-556">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-557">'Identity'</span></span>
- <span data-ttu-id="1bbfc-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-559">'Razor'</span></span>
- <span data-ttu-id="1bbfc-560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-562">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-563">'Identity'</span></span>
- <span data-ttu-id="1bbfc-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-565">'Razor'</span></span>
- <span data-ttu-id="1bbfc-566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-568">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-569">'Identity'</span></span>
- <span data-ttu-id="1bbfc-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-571">'Razor'</span></span>
- <span data-ttu-id="1bbfc-572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-574">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-575">'Identity'</span></span>
- <span data-ttu-id="1bbfc-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-577">'Razor'</span></span>
- <span data-ttu-id="1bbfc-578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-580">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-581">'Identity'</span></span>
- <span data-ttu-id="1bbfc-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-583">'Razor'</span></span>
- <span data-ttu-id="1bbfc-584">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-586">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-587">'Identity'</span></span>
- <span data-ttu-id="1bbfc-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-589">'Razor'</span></span>
- <span data-ttu-id="1bbfc-590">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-592">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-593">'Identity'</span></span>
- <span data-ttu-id="1bbfc-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-595">'Razor'</span></span>
- <span data-ttu-id="1bbfc-596">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-598">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-599">'Identity'</span></span>
- <span data-ttu-id="1bbfc-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-601">'Razor'</span></span>
- <span data-ttu-id="1bbfc-602">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-604">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-605">'Identity'</span></span>
- <span data-ttu-id="1bbfc-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-607">'Razor'</span></span>
- <span data-ttu-id="1bbfc-608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-610">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-611">'Identity'</span></span>
- <span data-ttu-id="1bbfc-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-613">'Razor'</span></span>
- <span data-ttu-id="1bbfc-614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-614">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-616">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-617">'Identity'</span></span>
- <span data-ttu-id="1bbfc-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-619">'Razor'</span></span>
- <span data-ttu-id="1bbfc-620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-622">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-623">'Identity'</span></span>
- <span data-ttu-id="1bbfc-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-625">'Razor'</span></span>
- <span data-ttu-id="1bbfc-626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-628">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-629">'Identity'</span></span>
- <span data-ttu-id="1bbfc-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-631">'Razor'</span></span>
- <span data-ttu-id="1bbfc-632">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-634">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-635">'Identity'</span></span>
- <span data-ttu-id="1bbfc-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-637">'Razor'</span></span>
- <span data-ttu-id="1bbfc-638">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-640">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-641">'Identity'</span></span>
- <span data-ttu-id="1bbfc-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-643">'Razor'</span></span>
- <span data-ttu-id="1bbfc-644">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-646">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-647">'Identity'</span></span>
- <span data-ttu-id="1bbfc-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-649">'Razor'</span></span>
- <span data-ttu-id="1bbfc-650">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-652">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-653">'Identity'</span></span>
- <span data-ttu-id="1bbfc-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-655">'Razor'</span></span>
- <span data-ttu-id="1bbfc-656">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-658">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-659">'Identity'</span></span>
- <span data-ttu-id="1bbfc-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-661">'Razor'</span></span>
- <span data-ttu-id="1bbfc-662">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-664">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-665">'Identity'</span></span>
- <span data-ttu-id="1bbfc-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-667">'Razor'</span></span>
- <span data-ttu-id="1bbfc-668">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-670">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-671">'Identity'</span></span>
- <span data-ttu-id="1bbfc-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-673">'Razor'</span></span>
- <span data-ttu-id="1bbfc-674">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-676">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-677">'Identity'</span></span>
- <span data-ttu-id="1bbfc-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-679">'Razor'</span></span>
- <span data-ttu-id="1bbfc-680">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-682">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-683">'Identity'</span></span>
- <span data-ttu-id="1bbfc-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-685">'Razor'</span></span>
- <span data-ttu-id="1bbfc-686">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-688">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-689">'Identity'</span></span>
- <span data-ttu-id="1bbfc-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-691">'Razor'</span></span>
- <span data-ttu-id="1bbfc-692">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-694">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-695">'Identity'</span></span>
- <span data-ttu-id="1bbfc-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-697">'Razor'</span></span>
- <span data-ttu-id="1bbfc-698">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-700">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-701">'Identity'</span></span>
- <span data-ttu-id="1bbfc-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-703">'Razor'</span></span>
- <span data-ttu-id="1bbfc-704">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-706">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-707">'Identity'</span></span>
- <span data-ttu-id="1bbfc-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-709">'Razor'</span></span>
- <span data-ttu-id="1bbfc-710">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-712">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-713">'Identity'</span></span>
- <span data-ttu-id="1bbfc-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-715">'Razor'</span></span>
- <span data-ttu-id="1bbfc-716">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-718">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-719">'Identity'</span></span>
- <span data-ttu-id="1bbfc-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-721">'Razor'</span></span>
- <span data-ttu-id="1bbfc-722">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-724">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-725">'Identity'</span></span>
- <span data-ttu-id="1bbfc-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-727">'Razor'</span></span>
- <span data-ttu-id="1bbfc-728">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-730">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-731">'Identity'</span></span>
- <span data-ttu-id="1bbfc-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-733">'Razor'</span></span>
- <span data-ttu-id="1bbfc-734">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-736">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-737">'Identity'</span></span>
- <span data-ttu-id="1bbfc-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-739">'Razor'</span></span>
- <span data-ttu-id="1bbfc-740">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-742">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-743">'Identity'</span></span>
- <span data-ttu-id="1bbfc-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-745">'Razor'</span></span>
- <span data-ttu-id="1bbfc-746">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-748">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-749">'Identity'</span></span>
- <span data-ttu-id="1bbfc-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-751">'Razor'</span></span>
- <span data-ttu-id="1bbfc-752">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-754">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-755">'Identity'</span></span>
- <span data-ttu-id="1bbfc-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-757">'Razor'</span></span>
- <span data-ttu-id="1bbfc-758">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-760">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-761">'Identity'</span></span>
- <span data-ttu-id="1bbfc-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-763">'Razor'</span></span>
- <span data-ttu-id="1bbfc-764">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-766">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-767">'Identity'</span></span>
- <span data-ttu-id="1bbfc-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-769">'Razor'</span></span>
- <span data-ttu-id="1bbfc-770">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-772">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-773">'Identity'</span></span>
- <span data-ttu-id="1bbfc-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-775">'Razor'</span></span>
- <span data-ttu-id="1bbfc-776">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-778">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-779">'Identity'</span></span>
- <span data-ttu-id="1bbfc-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-781">'Razor'</span></span>
- <span data-ttu-id="1bbfc-782">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-784">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-785">'Identity'</span></span>
- <span data-ttu-id="1bbfc-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-787">'Razor'</span></span>
- <span data-ttu-id="1bbfc-788">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-790">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-791">'Identity'</span></span>
- <span data-ttu-id="1bbfc-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-793">'Razor'</span></span>
- <span data-ttu-id="1bbfc-794">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-794">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-795">-------------------------------- | | `RuleResult.ContinueRules`(기본값) | 규칙 적용을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="1bbfc-796">| | `RuleResult.EndResponse`             | 규칙 적용을 중지하고 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="1bbfc-797">| | `RuleResult.SkipRemainingRules`      | 규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1bbfc-798">예제 응용 프로그램은 *.xml*로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1bbfc-799">`/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1bbfc-800">상태 코드는 *301 - 영구적으로 이동됨*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1bbfc-801">브라우저에서 */xmlfiles/file.xml*에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1bbfc-802">리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1bbfc-803">그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1bbfc-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1bbfc-805">또한 이 방법은 요청을 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="1bbfc-806">샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1bbfc-807">정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1bbfc-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1bbfc-809">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-809">IRule-based rule</span></span>

<span data-ttu-id="1bbfc-810"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1bbfc-811">`IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1bbfc-812">구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1bbfc-813">예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1bbfc-814">`extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png*, *.jpg*, 또는 *.gif* 중 하나이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1bbfc-815">만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1bbfc-816">*image.png*에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1bbfc-817">*image.jpg*에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1bbfc-818">상태 코드는 *301 - 영구적으로 이동됨*으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1bbfc-819">원본 요청: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-819">Original Request: `/image.png`</span></span>

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1bbfc-821">원본 요청: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-821">Original Request: `/image.jpg`</span></span>

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1bbfc-823">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-823">Regex examples</span></span>

| <span data-ttu-id="1bbfc-824">Goal</span><span class="sxs-lookup"><span data-stu-id="1bbfc-824">Goal</span></span> | <span data-ttu-id="1bbfc-825">정규식 문자열 및</span><span class="sxs-lookup"><span data-stu-id="1bbfc-825">Regex String &</span></span><br><span data-ttu-id="1bbfc-826">일치 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-826">Match Example</span></span> | <span data-ttu-id="1bbfc-827">대체 문자열 및</span><span class="sxs-lookup"><span data-stu-id="1bbfc-827">Replacement String &</span></span><br><span data-ttu-id="1bbfc-828">출력 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="1bbfc-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-830">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-831">'Identity'</span></span>
- <span data-ttu-id="1bbfc-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-833">'Razor'</span></span>
- <span data-ttu-id="1bbfc-834">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-836">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-837">'Identity'</span></span>
- <span data-ttu-id="1bbfc-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-839">'Razor'</span></span>
- <span data-ttu-id="1bbfc-840">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-842">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-843">'Identity'</span></span>
- <span data-ttu-id="1bbfc-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-845">'Razor'</span></span>
- <span data-ttu-id="1bbfc-846">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-848">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-849">'Identity'</span></span>
- <span data-ttu-id="1bbfc-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-851">'Razor'</span></span>
- <span data-ttu-id="1bbfc-852">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-854">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-855">'Identity'</span></span>
- <span data-ttu-id="1bbfc-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-857">'Razor'</span></span>
- <span data-ttu-id="1bbfc-858">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-860">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-861">'Identity'</span></span>
- <span data-ttu-id="1bbfc-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-863">'Razor'</span></span>
- <span data-ttu-id="1bbfc-864">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-866">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-867">'Identity'</span></span>
- <span data-ttu-id="1bbfc-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-869">'Razor'</span></span>
- <span data-ttu-id="1bbfc-870">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-872">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-873">'Identity'</span></span>
- <span data-ttu-id="1bbfc-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-875">'Razor'</span></span>
- <span data-ttu-id="1bbfc-876">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-878">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-879">'Identity'</span></span>
- <span data-ttu-id="1bbfc-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-881">'Razor'</span></span>
- <span data-ttu-id="1bbfc-882">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-884">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-885">'Identity'</span></span>
- <span data-ttu-id="1bbfc-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-887">'Razor'</span></span>
- <span data-ttu-id="1bbfc-888">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-890">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-891">'Identity'</span></span>
- <span data-ttu-id="1bbfc-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-893">'Razor'</span></span>
- <span data-ttu-id="1bbfc-894">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-896">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-897">'Identity'</span></span>
- <span data-ttu-id="1bbfc-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-899">'Razor'</span></span>
- <span data-ttu-id="1bbfc-900">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-902">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-903">'Identity'</span></span>
- <span data-ttu-id="1bbfc-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-905">'Razor'</span></span>
- <span data-ttu-id="1bbfc-906">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-906">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-908">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-909">'Identity'</span></span>
- <span data-ttu-id="1bbfc-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-911">'Razor'</span></span>
- <span data-ttu-id="1bbfc-912">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-914">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-915">'Identity'</span></span>
- <span data-ttu-id="1bbfc-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-917">'Razor'</span></span>
- <span data-ttu-id="1bbfc-918">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-920">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-921">'Identity'</span></span>
- <span data-ttu-id="1bbfc-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-923">'Razor'</span></span>
- <span data-ttu-id="1bbfc-924">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-926">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-927">'Identity'</span></span>
- <span data-ttu-id="1bbfc-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-929">'Razor'</span></span>
- <span data-ttu-id="1bbfc-930">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-932">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-933">'Identity'</span></span>
- <span data-ttu-id="1bbfc-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-935">'Razor'</span></span>
- <span data-ttu-id="1bbfc-936">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-938">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-939">'Identity'</span></span>
- <span data-ttu-id="1bbfc-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-941">'Razor'</span></span>
- <span data-ttu-id="1bbfc-942">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-944">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-945">'Identity'</span></span>
- <span data-ttu-id="1bbfc-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-947">'Razor'</span></span>
- <span data-ttu-id="1bbfc-948">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-950">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-951">'Identity'</span></span>
- <span data-ttu-id="1bbfc-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-953">'Razor'</span></span>
- <span data-ttu-id="1bbfc-954">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-956">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-957">'Identity'</span></span>
- <span data-ttu-id="1bbfc-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-959">'Razor'</span></span>
- <span data-ttu-id="1bbfc-960">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-962">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-963">'Identity'</span></span>
- <span data-ttu-id="1bbfc-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-965">'Razor'</span></span>
- <span data-ttu-id="1bbfc-966">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-968">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-969">'Identity'</span></span>
- <span data-ttu-id="1bbfc-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-971">'Razor'</span></span>
- <span data-ttu-id="1bbfc-972">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-974">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-975">'Identity'</span></span>
- <span data-ttu-id="1bbfc-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-977">'Razor'</span></span>
- <span data-ttu-id="1bbfc-978">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-980">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-981">'Identity'</span></span>
- <span data-ttu-id="1bbfc-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-983">'Razor'</span></span>
- <span data-ttu-id="1bbfc-984">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-986">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-987">'Identity'</span></span>
- <span data-ttu-id="1bbfc-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-989">'Razor'</span></span>
- <span data-ttu-id="1bbfc-990">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-992">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-993">'Identity'</span></span>
- <span data-ttu-id="1bbfc-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-995">'Razor'</span></span>
- <span data-ttu-id="1bbfc-996">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-998">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-999">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1001">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1002">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1004">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1005">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1007">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1008">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1009">------------------- | | 경로를 쿼리 문자열로 재작성 | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="1bbfc-1010">`/path?var1=abc&var2=123` | | 후행 슬래시 제거 | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="1bbfc-1011">`/path` | | 후행 슬래시 적용 | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="1bbfc-1012">`/path/` | | 특정 요청 재작성 방지 | `^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1bbfc-1013">예: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1bbfc-1014">아니요: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="1bbfc-1015">`/resource.axd` | | URL 세그먼트 재정렬 | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="1bbfc-1016">`path/3/2/1` | | URL 세그먼트 대체 | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bbfc-1017">본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1bbfc-1018">URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1bbfc-1019">URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1bbfc-1020">URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1bbfc-1021">서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1bbfc-1022">여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1bbfc-1023">들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1bbfc-1024">SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1bbfc-1025">친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1bbfc-1026">안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1bbfc-1027">외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-1028">URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1bbfc-1029">가능한 경우 규칙의 수와 복잡성을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1bbfc-1030">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1bbfc-1031">URL 리디렉션 및 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1bbfc-1032">*URL 리디렉션*과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1bbfc-1033">ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1bbfc-1034">*URL 리디렉션*은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1bbfc-1035">이 경우 서버를 왕복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="1bbfc-1036">클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1bbfc-1037">`/resource`가 `/different-resource`로 *리디렉션*되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1bbfc-1043">요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1bbfc-1044">*301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1bbfc-1045">*301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1bbfc-1046">*302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1bbfc-1047">302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1bbfc-1048">상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1bbfc-1049">*URL 재작성*은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1bbfc-1050">URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1bbfc-1051">다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1bbfc-1052">`/resource`가 `/different-resource`에 *다시 작성*되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1bbfc-1053">클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1bbfc-1058">URL 재작성 예제 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1058">URL rewriting sample app</span></span>

<span data-ttu-id="1bbfc-1059">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1bbfc-1060">이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1bbfc-1061">URL 재작성 미들웨어를 사용해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1bbfc-1062">다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* <span data-ttu-id="1bbfc-1063">Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1063">[URL Rewrite module with IIS on Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)</span></span>
* <span data-ttu-id="1bbfc-1064">Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1064">[Apache mod_rewrite module on Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* [<span data-ttu-id="1bbfc-1065">Nginx의 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1bbfc-1066">또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1bbfc-1067">IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1bbfc-1068">미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1bbfc-1069">서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1bbfc-1070">바로 이런 시나리오에서 대신 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1bbfc-1071">미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1bbfc-1072">벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1bbfc-1073">패키지</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1073">Package</span></span>

<span data-ttu-id="1bbfc-1074">프로젝트에 미들웨어를 포함시키려면 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지가 포함된 프로젝트 파일의 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="1bbfc-1075">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 `Microsoft.AspNetCore.Rewrite` 패키지에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1bbfc-1076">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1076">Extension and options</span></span>

<span data-ttu-id="1bbfc-1077">각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1bbfc-1078">처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1bbfc-1079">`RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1bbfc-1080">www 이외 요청을 www로 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1080">Redirect non-www to www</span></span>

<span data-ttu-id="1bbfc-1081">이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1bbfc-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1bbfc-1083">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1bbfc-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1bbfc-1085">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1bbfc-1086">오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1bbfc-1087">상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1bbfc-1088">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1088">URL redirect</span></span>

<span data-ttu-id="1bbfc-1089">요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1bbfc-1090">첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1bbfc-1091">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="1bbfc-1092">필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1bbfc-1093">상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음*으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1bbfc-1094">개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1bbfc-1095">그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1bbfc-1096">리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1bbfc-1097">브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1bbfc-1098">샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1bbfc-1099">두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1bbfc-1100">응답 본문에 리디렉션 URL이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1bbfc-1101">URL이 *리디렉션*되면 서버로의 왕복이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1bbfc-1102">리디렉션 규칙을 설정할 때에는 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1bbfc-1103">리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1bbfc-1104">따라서 *무한 리디렉션 루프*를 실수로 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1bbfc-1105">원본 요청: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1bbfc-1107">표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1bbfc-1108">그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1bbfc-1109">마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1bbfc-1110">따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1bbfc-1111">즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1bbfc-1112">대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1bbfc-1113">첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1bbfc-1114">예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1bbfc-1115">규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1bbfc-1116">보안 엔드포인트에 대한 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1bbfc-1117"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1bbfc-1118">상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1bbfc-1119">포트가 제공되지 않는 경우 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="1bbfc-1120">미들웨어가 기본적으로 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1bbfc-1121">체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1bbfc-1122">다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨*으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1bbfc-1123"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1bbfc-1124">미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨*으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1bbfc-1125">추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1bbfc-1126">자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1bbfc-1127">예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1bbfc-1128">먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1bbfc-1129">모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1bbfc-1130">자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1bbfc-1131">`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1bbfc-1133">`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1bbfc-1135">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1135">URL rewrite</span></span>

<span data-ttu-id="1bbfc-1136">URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1bbfc-1137">첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1bbfc-1138">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="1bbfc-1139">세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1bbfc-1140">원래 요청: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1bbfc-1142">식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1bbfc-1143">`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1bbfc-1144">따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1bbfc-1145">경로</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1145">Path</span></span>                               | <span data-ttu-id="1bbfc-1146">일치</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1146">Match</span></span> |
| ---
<span data-ttu-id="1bbfc-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1148">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1149">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1151">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1152">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1154">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1155">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1157">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1158">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1160">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1161">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1163">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1164">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1166">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1167">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1169">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1170">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1172">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1173">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1175">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1176">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1178">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1179">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1181">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1182">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1184">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1185">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1187">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1188">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1190">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1191">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1193">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1194">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1196">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1197">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1199">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1200">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1202">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1203">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1205">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1206">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1208">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1209">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1211">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1212">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1214">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1215">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1217">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1218">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1220">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1221">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1223">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1224">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1226">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1227">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1229">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1230">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1232">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1233">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1235">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1236">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | 예   | | `/my-cool-redirect-rule/1234/5678` | 예   | | `/anotherredirect-rule/1234/5678`  | 예   |</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="1bbfc-1238">반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1bbfc-1239">다음 표에는 일치에서의 차이가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1bbfc-1240">경로</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1240">Path</span></span>                              | <span data-ttu-id="1bbfc-1241">일치</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1241">Match</span></span> |
| ---
<span data-ttu-id="1bbfc-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1243">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1244">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1246">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1247">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1249">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1250">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1252">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1253">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1255">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1256">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1258">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1259">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1261">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1262">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1264">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1265">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1267">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1268">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1270">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1271">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1273">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1274">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1276">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1277">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1279">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1280">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1282">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1283">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1285">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1286">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1288">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1289">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1291">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1292">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1294">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1295">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1297">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1298">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1300">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1301">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1303">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1304">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1306">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1307">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1309">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1310">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1312">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1313">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1315">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1316">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1318">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1319">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1321">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1322">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1324">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1325">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | 예   | | `/my-cool-rewrite-rule/1234/5678` | 아니요    | | `/anotherrewrite-rule/1234/5678`  | 아니요    |</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="1bbfc-1327">표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1bbfc-1328">여기서 `\d`는 *숫자 하나와 일치*함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1bbfc-1329">그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치*함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1bbfc-1330">따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1bbfc-1331">이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1bbfc-1332">재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1bbfc-1333">즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1bbfc-1334">원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1bbfc-1335">리소스를 가져오기 위해 서버를 왕복하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1bbfc-1336">리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1bbfc-1337">클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1bbfc-1338">클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-1339">일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1bbfc-1340">최대한 빠른 응용 프로그램 응답을 위해서는:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1bbfc-1341">재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1bbfc-1342">일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1bbfc-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="1bbfc-1344"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1bbfc-1345">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1bbfc-1346">mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1bbfc-1347"><xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1bbfc-1348">예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1bbfc-1349">응답 상태 코드는 *302 - 있음*입니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1bbfc-1350">원본 요청: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1bbfc-1352">미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1bbfc-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1bbfc-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1bbfc-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="1bbfc-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1bbfc-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1358">HTTP_HOST</span></span>
* <span data-ttu-id="1bbfc-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="1bbfc-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1bbfc-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1361">HTTPS</span></span>
* <span data-ttu-id="1bbfc-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1362">IPV6</span></span>
* <span data-ttu-id="1bbfc-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1363">QUERY_STRING</span></span>
* <span data-ttu-id="1bbfc-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="1bbfc-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1bbfc-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="1bbfc-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1bbfc-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1369">REQUEST_URI</span></span>
* <span data-ttu-id="1bbfc-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1bbfc-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="1bbfc-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1372">SERVER_PORT</span></span>
* <span data-ttu-id="1bbfc-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1bbfc-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1374">TIME</span></span>
* <span data-ttu-id="1bbfc-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1375">TIME_DAY</span></span>
* <span data-ttu-id="1bbfc-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1376">TIME_HOUR</span></span>
* <span data-ttu-id="1bbfc-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1377">TIME_MIN</span></span>
* <span data-ttu-id="1bbfc-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1378">TIME_MON</span></span>
* <span data-ttu-id="1bbfc-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1379">TIME_SEC</span></span>
* <span data-ttu-id="1bbfc-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1380">TIME_WDAY</span></span>
* <span data-ttu-id="1bbfc-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1bbfc-1382">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1bbfc-1383">IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1bbfc-1384">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1bbfc-1385">Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1bbfc-1386">IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1bbfc-1387">IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1bbfc-1388"><xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1bbfc-1389">예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1bbfc-1390">응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1bbfc-1391">원본 요청: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1bbfc-1393">원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1bbfc-1394">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1bbfc-1395">지원되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1395">Unsupported features</span></span>

<span data-ttu-id="1bbfc-1396">ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1bbfc-1397">아웃바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1397">Outbound Rules</span></span>
* <span data-ttu-id="1bbfc-1398">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1398">Custom Server Variables</span></span>
* <span data-ttu-id="1bbfc-1399">와일드카드</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1399">Wildcards</span></span>
* <span data-ttu-id="1bbfc-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1bbfc-1401">지원되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1401">Supported server variables</span></span>

<span data-ttu-id="1bbfc-1402">미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1bbfc-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1bbfc-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="1bbfc-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1bbfc-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1bbfc-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="1bbfc-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1408">HTTP_HOST</span></span>
* <span data-ttu-id="1bbfc-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="1bbfc-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1410">HTTP_URL</span></span>
* <span data-ttu-id="1bbfc-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1bbfc-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1412">HTTPS</span></span>
* <span data-ttu-id="1bbfc-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="1bbfc-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1414">QUERY_STRING</span></span>
* <span data-ttu-id="1bbfc-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="1bbfc-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="1bbfc-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1bbfc-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1bbfc-1419"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1bbfc-1420">이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1bbfc-1421">재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1bbfc-1422">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1422">Method-based rule</span></span>

<span data-ttu-id="1bbfc-1423">메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1bbfc-1424">`Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1bbfc-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1bbfc-1426">값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="1bbfc-1427">작업</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="1bbfc-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1429">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1430">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1432">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1433">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1435">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1436">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1438">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1439">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1441">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1442">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1444">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1445">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1447">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1448">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1450">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1451">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1453">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1454">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1456">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1457">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1459">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1460">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1462">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1463">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1465">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1466">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1468">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1469">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1471">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1472">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1474">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1475">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1477">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1478">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1480">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1481">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1483">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1484">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1486">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1487">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1489">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1490">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1492">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1493">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1495">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1496">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1498">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1499">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1501">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1502">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1504">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1505">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1507">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1508">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1510">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1511">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1513">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1514">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1516">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1517">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1519">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1520">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1522">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1523">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1525">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1526">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1528">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1529">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1531">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1532">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1534">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1535">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1537">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1538">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1540">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1541">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1543">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1544">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1546">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1547">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1549">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1550">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1552">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1553">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1555">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1556">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1558">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1559">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1561">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1562">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1564">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1565">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1567">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1568">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1570">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1571">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1573">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1574">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1576">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1577">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1579">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1580">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1582">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1583">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1585">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1586">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1588">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1589">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1591">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1592">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1594">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1595">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1597">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1598">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1600">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1601">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1603">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1604">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1606">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1607">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1609">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1610">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1612">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1613">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1615">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1616">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1618">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1619">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1621">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1622">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1624">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1625">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1627">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1628">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1630">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1631">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1633">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1634">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1636">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1637">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1639">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1640">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1642">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1643">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1645">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1646">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1648">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1649">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1651">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1652">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1654">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1655">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1657">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1658">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1660">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1661">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1663">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1664">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1666">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1667">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1669">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1670">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1672">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1673">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1675">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1676">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1678">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1679">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1681">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1682">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1684">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1685">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1687">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1688">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1690">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1691">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1693">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1694">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1696">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1697">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1699">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1700">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1702">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1703">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1704">-------------------------------- | | `RuleResult.ContinueRules`(기본값) | 규칙 적용을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="1bbfc-1705">| | `RuleResult.EndResponse`             | 규칙 적용을 중지하고 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="1bbfc-1706">| | `RuleResult.SkipRemainingRules`      | 규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1bbfc-1707">예제 응용 프로그램은 *.xml*로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1bbfc-1708">`/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1bbfc-1709">상태 코드는 *301 - 영구적으로 이동됨*으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1bbfc-1710">브라우저에서 */xmlfiles/file.xml*에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1bbfc-1711">리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1bbfc-1712">그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1bbfc-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1bbfc-1714">또한 이 방법은 요청을 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="1bbfc-1715">샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1bbfc-1716">정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1bbfc-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1bbfc-1718">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1718">IRule-based rule</span></span>

<span data-ttu-id="1bbfc-1719"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1bbfc-1720">`IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1bbfc-1721">구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1bbfc-1722">예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1bbfc-1723">`extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png*, *.jpg*, 또는 *.gif* 중 하나이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1bbfc-1724">만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1bbfc-1725">*image.png*에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1bbfc-1726">*image.jpg*에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1bbfc-1727">상태 코드는 *301 - 영구적으로 이동됨*으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1bbfc-1728">원본 요청: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1728">Original Request: `/image.png`</span></span>

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1bbfc-1730">원본 요청: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1730">Original Request: `/image.jpg`</span></span>

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1bbfc-1732">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1732">Regex examples</span></span>

| <span data-ttu-id="1bbfc-1733">Goal</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1733">Goal</span></span> | <span data-ttu-id="1bbfc-1734">정규식 문자열 및</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1734">Regex String &</span></span><br><span data-ttu-id="1bbfc-1735">일치 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1735">Match Example</span></span> | <span data-ttu-id="1bbfc-1736">대체 문자열 및</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1736">Replacement String &</span></span><br><span data-ttu-id="1bbfc-1737">출력 예제</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="1bbfc-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1739">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1740">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1742">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1743">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1745">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1746">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1748">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1749">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1751">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1752">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1754">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1755">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1757">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1758">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1760">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1761">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1763">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1764">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1766">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1767">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1769">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1770">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1772">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1773">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1775">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1776">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1778">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1779">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1781">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1782">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1784">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1785">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1787">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1788">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1790">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1791">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1793">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1794">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1796">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1797">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1799">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1800">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1802">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1803">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1805">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1806">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1808">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1809">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1811">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1812">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1814">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1815">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1817">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1818">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1820">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1821">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1823">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1824">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1826">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1827">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1829">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1830">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1832">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1833">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1835">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1836">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1838">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1839">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1841">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1842">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1844">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1845">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1847">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1848">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1850">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1851">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1853">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1854">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1856">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1857">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1859">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1860">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1862">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1863">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1865">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1866">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1868">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1869">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1871">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1872">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1874">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1875">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1877">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1878">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1880">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1881">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1883">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1884">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1886">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1887">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1889">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1890">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1892">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1893">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1895">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1896">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1898">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1899">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1901">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1902">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1904">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1905">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1907">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1908">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1910">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1911">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1bbfc-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1bbfc-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1913">'Blazor'</span></span>
- <span data-ttu-id="1bbfc-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1914">'Identity'</span></span>
- <span data-ttu-id="1bbfc-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="1bbfc-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1916">'Razor'</span></span>
- <span data-ttu-id="1bbfc-1917">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="1bbfc-1918">------------------- | | 경로를 쿼리 문자열로 재작성 | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="1bbfc-1919">`/path?var1=abc&var2=123` | | 후행 슬래시 제거 | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="1bbfc-1920">`/path` | | 후행 슬래시 적용 | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="1bbfc-1921">`/path/` | | 특정 요청 재작성 방지 | `^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1bbfc-1922">예: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1bbfc-1923">아니요: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="1bbfc-1924">`/resource.axd` | | URL 세그먼트 재정렬 | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="1bbfc-1925">`path/3/2/1` | | URL 세그먼트 대체 | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1bbfc-1926">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1bbfc-1927">.NET에서의 정규식</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="1bbfc-1928">정규식 언어 - 빠른 참조</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="1bbfc-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1bbfc-1930">Url 재작성 모듈 2.0 사용(IIS용)</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="1bbfc-1931">URL 재작성 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="1bbfc-1932">IIS URL 재작성 모듈 포럼</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="1bbfc-1933">간단한 URL 구조 유지</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="1bbfc-1934">10가지 URL 재작성 팁과 요령</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="1bbfc-1935">슬래시 여부</span><span class="sxs-lookup"><span data-stu-id="1bbfc-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
