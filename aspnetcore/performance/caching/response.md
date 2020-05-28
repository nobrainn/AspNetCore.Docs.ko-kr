---
<span data-ttu-id="6102e-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-102">'Blazor'</span></span>
- <span data-ttu-id="6102e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-103">'Identity'</span></span>
- <span data-ttu-id="6102e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-105">'Razor'</span></span>
- <span data-ttu-id="6102e-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="6102e-107">ASP.NET Core의 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="6102e-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="6102e-108">작성자, [John 루 오 어](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6102e-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6102e-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6102e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6102e-110">응답 캐싱은 클라이언트 또는 프록시가 웹 서버에 대해 수행 하는 요청 수를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="6102e-111">응답 캐싱은 웹 서버에서 응답을 생성 하기 위해 수행 하는 작업의 양을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="6102e-112">응답 캐싱은 클라이언트, 프록시 및 미들웨어에서 응답을 캐시 하는 방법을 지정 하는 헤더에 의해 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="6102e-113">[ResponseCache 특성](#responsecache-attribute) 은 응답 캐싱 헤더 설정에 참여 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="6102e-114">클라이언트 및 중간 프록시는 [HTTP 1.1 캐싱 사양](https://tools.ietf.org/html/rfc7234)에 따라 응답을 캐시 하는 데 헤더를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="6102e-115">HTTP 1.1 캐싱 사양을 따르는 서버 쪽 캐싱의 경우 [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="6102e-116">미들웨어는 속성을 사용 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 하 여 서버 쪽 캐싱 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="6102e-117">HTTP 기반 응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="6102e-117">HTTP-based response caching</span></span>

<span data-ttu-id="6102e-118">[HTTP 1.1 캐싱 사양은](https://tools.ietf.org/html/rfc7234) 인터넷 캐시가 동작 하는 방식을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="6102e-119">캐싱에 사용 되는 기본 HTTP 헤더는 캐시 *지시문*을 지정 하는 데 사용 되는 [cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="6102e-120">지시문은 요청이 클라이언트에서 서버로 이동 하 고 응답을 통해 서버에서 클라이언트로 다시 이동 하는 방식으로 캐싱 동작을 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="6102e-121">요청 및 응답은 프록시 서버를 통해 이동 하 고, 프록시 서버는 HTTP 1.1 캐싱 사양을 준수 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="6102e-122">`Cache-Control`다음 표에서는 일반적인 지시문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="6102e-123">지시문</span><span class="sxs-lookup"><span data-stu-id="6102e-123">Directive</span></span>                                                       | <span data-ttu-id="6102e-124">작업</span><span class="sxs-lookup"><span data-stu-id="6102e-124">Action</span></span> |
| ---
<span data-ttu-id="6102e-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-126">'Blazor'</span></span>
- <span data-ttu-id="6102e-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-127">'Identity'</span></span>
- <span data-ttu-id="6102e-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-129">'Razor'</span></span>
- <span data-ttu-id="6102e-130">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-132">'Blazor'</span></span>
- <span data-ttu-id="6102e-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-133">'Identity'</span></span>
- <span data-ttu-id="6102e-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-135">'Razor'</span></span>
- <span data-ttu-id="6102e-136">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-138">'Blazor'</span></span>
- <span data-ttu-id="6102e-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-139">'Identity'</span></span>
- <span data-ttu-id="6102e-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-141">'Razor'</span></span>
- <span data-ttu-id="6102e-142">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-144">'Blazor'</span></span>
- <span data-ttu-id="6102e-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-145">'Identity'</span></span>
- <span data-ttu-id="6102e-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-147">'Razor'</span></span>
- <span data-ttu-id="6102e-148">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-150">'Blazor'</span></span>
- <span data-ttu-id="6102e-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-151">'Identity'</span></span>
- <span data-ttu-id="6102e-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-153">'Razor'</span></span>
- <span data-ttu-id="6102e-154">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-156">'Blazor'</span></span>
- <span data-ttu-id="6102e-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-157">'Identity'</span></span>
- <span data-ttu-id="6102e-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-159">'Razor'</span></span>
- <span data-ttu-id="6102e-160">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-162">'Blazor'</span></span>
- <span data-ttu-id="6102e-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-163">'Identity'</span></span>
- <span data-ttu-id="6102e-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-165">'Razor'</span></span>
- <span data-ttu-id="6102e-166">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-168">'Blazor'</span></span>
- <span data-ttu-id="6102e-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-169">'Identity'</span></span>
- <span data-ttu-id="6102e-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-171">'Razor'</span></span>
- <span data-ttu-id="6102e-172">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-174">'Blazor'</span></span>
- <span data-ttu-id="6102e-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-175">'Identity'</span></span>
- <span data-ttu-id="6102e-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-177">'Razor'</span></span>
- <span data-ttu-id="6102e-178">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-180">'Blazor'</span></span>
- <span data-ttu-id="6102e-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-181">'Identity'</span></span>
- <span data-ttu-id="6102e-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-183">'Razor'</span></span>
- <span data-ttu-id="6102e-184">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-186">'Blazor'</span></span>
- <span data-ttu-id="6102e-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-187">'Identity'</span></span>
- <span data-ttu-id="6102e-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-189">'Razor'</span></span>
- <span data-ttu-id="6102e-190">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-192">'Blazor'</span></span>
- <span data-ttu-id="6102e-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-193">'Identity'</span></span>
- <span data-ttu-id="6102e-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-195">'Razor'</span></span>
- <span data-ttu-id="6102e-196">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-198">'Blazor'</span></span>
- <span data-ttu-id="6102e-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-199">'Identity'</span></span>
- <span data-ttu-id="6102e-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-201">'Razor'</span></span>
- <span data-ttu-id="6102e-202">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-204">'Blazor'</span></span>
- <span data-ttu-id="6102e-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-205">'Identity'</span></span>
- <span data-ttu-id="6102e-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-207">'Razor'</span></span>
- <span data-ttu-id="6102e-208">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-210">'Blazor'</span></span>
- <span data-ttu-id="6102e-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-211">'Identity'</span></span>
- <span data-ttu-id="6102e-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-213">'Razor'</span></span>
- <span data-ttu-id="6102e-214">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-216">'Blazor'</span></span>
- <span data-ttu-id="6102e-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-217">'Identity'</span></span>
- <span data-ttu-id="6102e-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-219">'Razor'</span></span>
- <span data-ttu-id="6102e-220">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-222">'Blazor'</span></span>
- <span data-ttu-id="6102e-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-223">'Identity'</span></span>
- <span data-ttu-id="6102e-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-225">'Razor'</span></span>
- <span data-ttu-id="6102e-226">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-228">'Blazor'</span></span>
- <span data-ttu-id="6102e-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-229">'Identity'</span></span>
- <span data-ttu-id="6102e-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-231">'Razor'</span></span>
- <span data-ttu-id="6102e-232">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-234">'Blazor'</span></span>
- <span data-ttu-id="6102e-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-235">'Identity'</span></span>
- <span data-ttu-id="6102e-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-237">'Razor'</span></span>
- <span data-ttu-id="6102e-238">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-240">'Blazor'</span></span>
- <span data-ttu-id="6102e-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-241">'Identity'</span></span>
- <span data-ttu-id="6102e-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-243">'Razor'</span></span>
- <span data-ttu-id="6102e-244">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-246">'Blazor'</span></span>
- <span data-ttu-id="6102e-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-247">'Identity'</span></span>
- <span data-ttu-id="6102e-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-249">'Razor'</span></span>
- <span data-ttu-id="6102e-250">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-252">'Blazor'</span></span>
- <span data-ttu-id="6102e-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-253">'Identity'</span></span>
- <span data-ttu-id="6102e-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-255">'Razor'</span></span>
- <span data-ttu-id="6102e-256">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-258">'Blazor'</span></span>
- <span data-ttu-id="6102e-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-259">'Identity'</span></span>
- <span data-ttu-id="6102e-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-261">'Razor'</span></span>
- <span data-ttu-id="6102e-262">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-264">'Blazor'</span></span>
- <span data-ttu-id="6102e-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-265">'Identity'</span></span>
- <span data-ttu-id="6102e-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-267">'Razor'</span></span>
- <span data-ttu-id="6102e-268">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-270">'Blazor'</span></span>
- <span data-ttu-id="6102e-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-271">'Identity'</span></span>
- <span data-ttu-id="6102e-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-273">'Razor'</span></span>
- <span data-ttu-id="6102e-274">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-276">'Blazor'</span></span>
- <span data-ttu-id="6102e-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-277">'Identity'</span></span>
- <span data-ttu-id="6102e-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-279">'Razor'</span></span>
- <span data-ttu-id="6102e-280">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-282">'Blazor'</span></span>
- <span data-ttu-id="6102e-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-283">'Identity'</span></span>
- <span data-ttu-id="6102e-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-285">'Razor'</span></span>
- <span data-ttu-id="6102e-286">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-288">'Blazor'</span></span>
- <span data-ttu-id="6102e-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-289">'Identity'</span></span>
- <span data-ttu-id="6102e-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-291">'Razor'</span></span>
- <span data-ttu-id="6102e-292">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-294">'Blazor'</span></span>
- <span data-ttu-id="6102e-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-295">'Identity'</span></span>
- <span data-ttu-id="6102e-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-297">'Razor'</span></span>
- <span data-ttu-id="6102e-298">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-298">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-299">-------------------------------- | ---제목: author: 설명: monikerRange: ms: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-300">'Blazor'</span></span>
- <span data-ttu-id="6102e-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-301">'Identity'</span></span>
- <span data-ttu-id="6102e-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-303">'Razor'</span></span>
- <span data-ttu-id="6102e-304">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-304">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-305">--- | | [공용](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | 캐시에서 응답을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="6102e-306">| | [비공개](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | 응답은 공유 캐시에 저장 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="6102e-307">개인 캐시는 응답을 저장 하 고 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="6102e-308">| | [최대 사용 기간](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | 클라이언트는 나이가 지정 된 시간 (초) 보다 큰 응답을 수락 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="6102e-309">예: `max-age=60` (60 초), `max-age=2592000` (1 개월) | | [캐시 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **요청 시**: 캐시가 저장 된 응답을 사용 하 여 요청을 충족 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="6102e-310">원본 서버는 클라이언트에 대 한 응답을 다시 생성 하 고 미들웨어는 캐시에 저장 된 응답을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="6102e-311">**응답 시**: 원본 서버에서 유효성 검사 없이 후속 요청에 대 한 응답을 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="6102e-312">| | [저장소 없음](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **요청 시**: 캐시에서 요청을 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="6102e-313">**응답 시**: 캐시가 응답의 일부를 저장 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="6102e-314">캐시에서 역할을 수행 하는 다른 캐시 헤더는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="6102e-315">헤더</span><span class="sxs-lookup"><span data-stu-id="6102e-315">Header</span></span>                                                     | <span data-ttu-id="6102e-316">함수</span><span class="sxs-lookup"><span data-stu-id="6102e-316">Function</span></span> |
| ---
<span data-ttu-id="6102e-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-318">'Blazor'</span></span>
- <span data-ttu-id="6102e-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-319">'Identity'</span></span>
- <span data-ttu-id="6102e-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-321">'Razor'</span></span>
- <span data-ttu-id="6102e-322">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-324">'Blazor'</span></span>
- <span data-ttu-id="6102e-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-325">'Identity'</span></span>
- <span data-ttu-id="6102e-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-327">'Razor'</span></span>
- <span data-ttu-id="6102e-328">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-330">'Blazor'</span></span>
- <span data-ttu-id="6102e-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-331">'Identity'</span></span>
- <span data-ttu-id="6102e-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-333">'Razor'</span></span>
- <span data-ttu-id="6102e-334">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-336">'Blazor'</span></span>
- <span data-ttu-id="6102e-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-337">'Identity'</span></span>
- <span data-ttu-id="6102e-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-339">'Razor'</span></span>
- <span data-ttu-id="6102e-340">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-342">'Blazor'</span></span>
- <span data-ttu-id="6102e-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-343">'Identity'</span></span>
- <span data-ttu-id="6102e-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-345">'Razor'</span></span>
- <span data-ttu-id="6102e-346">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-348">'Blazor'</span></span>
- <span data-ttu-id="6102e-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-349">'Identity'</span></span>
- <span data-ttu-id="6102e-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-351">'Razor'</span></span>
- <span data-ttu-id="6102e-352">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-354">'Blazor'</span></span>
- <span data-ttu-id="6102e-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-355">'Identity'</span></span>
- <span data-ttu-id="6102e-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-357">'Razor'</span></span>
- <span data-ttu-id="6102e-358">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-360">'Blazor'</span></span>
- <span data-ttu-id="6102e-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-361">'Identity'</span></span>
- <span data-ttu-id="6102e-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-363">'Razor'</span></span>
- <span data-ttu-id="6102e-364">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-366">'Blazor'</span></span>
- <span data-ttu-id="6102e-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-367">'Identity'</span></span>
- <span data-ttu-id="6102e-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-369">'Razor'</span></span>
- <span data-ttu-id="6102e-370">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-372">'Blazor'</span></span>
- <span data-ttu-id="6102e-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-373">'Identity'</span></span>
- <span data-ttu-id="6102e-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-375">'Razor'</span></span>
- <span data-ttu-id="6102e-376">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-378">'Blazor'</span></span>
- <span data-ttu-id="6102e-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-379">'Identity'</span></span>
- <span data-ttu-id="6102e-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-381">'Razor'</span></span>
- <span data-ttu-id="6102e-382">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-384">'Blazor'</span></span>
- <span data-ttu-id="6102e-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-385">'Identity'</span></span>
- <span data-ttu-id="6102e-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-387">'Razor'</span></span>
- <span data-ttu-id="6102e-388">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-390">'Blazor'</span></span>
- <span data-ttu-id="6102e-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-391">'Identity'</span></span>
- <span data-ttu-id="6102e-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-393">'Razor'</span></span>
- <span data-ttu-id="6102e-394">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-396">'Blazor'</span></span>
- <span data-ttu-id="6102e-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-397">'Identity'</span></span>
- <span data-ttu-id="6102e-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-399">'Razor'</span></span>
- <span data-ttu-id="6102e-400">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-402">'Blazor'</span></span>
- <span data-ttu-id="6102e-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-403">'Identity'</span></span>
- <span data-ttu-id="6102e-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-405">'Razor'</span></span>
- <span data-ttu-id="6102e-406">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-408">'Blazor'</span></span>
- <span data-ttu-id="6102e-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-409">'Identity'</span></span>
- <span data-ttu-id="6102e-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-411">'Razor'</span></span>
- <span data-ttu-id="6102e-412">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-414">'Blazor'</span></span>
- <span data-ttu-id="6102e-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-415">'Identity'</span></span>
- <span data-ttu-id="6102e-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-417">'Razor'</span></span>
- <span data-ttu-id="6102e-418">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-420">'Blazor'</span></span>
- <span data-ttu-id="6102e-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-421">'Identity'</span></span>
- <span data-ttu-id="6102e-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-423">'Razor'</span></span>
- <span data-ttu-id="6102e-424">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-426">'Blazor'</span></span>
- <span data-ttu-id="6102e-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-427">'Identity'</span></span>
- <span data-ttu-id="6102e-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-429">'Razor'</span></span>
- <span data-ttu-id="6102e-430">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-432">'Blazor'</span></span>
- <span data-ttu-id="6102e-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-433">'Identity'</span></span>
- <span data-ttu-id="6102e-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-435">'Razor'</span></span>
- <span data-ttu-id="6102e-436">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-438">'Blazor'</span></span>
- <span data-ttu-id="6102e-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-439">'Identity'</span></span>
- <span data-ttu-id="6102e-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-441">'Razor'</span></span>
- <span data-ttu-id="6102e-442">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-444">'Blazor'</span></span>
- <span data-ttu-id="6102e-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-445">'Identity'</span></span>
- <span data-ttu-id="6102e-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-447">'Razor'</span></span>
- <span data-ttu-id="6102e-448">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-450">'Blazor'</span></span>
- <span data-ttu-id="6102e-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-451">'Identity'</span></span>
- <span data-ttu-id="6102e-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-453">'Razor'</span></span>
- <span data-ttu-id="6102e-454">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-456">'Blazor'</span></span>
- <span data-ttu-id="6102e-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-457">'Identity'</span></span>
- <span data-ttu-id="6102e-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-459">'Razor'</span></span>
- <span data-ttu-id="6102e-460">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-462">'Blazor'</span></span>
- <span data-ttu-id="6102e-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-463">'Identity'</span></span>
- <span data-ttu-id="6102e-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-465">'Razor'</span></span>
- <span data-ttu-id="6102e-466">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-468">'Blazor'</span></span>
- <span data-ttu-id="6102e-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-469">'Identity'</span></span>
- <span data-ttu-id="6102e-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-471">'Razor'</span></span>
- <span data-ttu-id="6102e-472">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-474">'Blazor'</span></span>
- <span data-ttu-id="6102e-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-475">'Identity'</span></span>
- <span data-ttu-id="6102e-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-477">'Razor'</span></span>
- <span data-ttu-id="6102e-478">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-478">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-479">----------------------------- | ---제목: author: 설명: monikerRange: ms: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-480">'Blazor'</span></span>
- <span data-ttu-id="6102e-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-481">'Identity'</span></span>
- <span data-ttu-id="6102e-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-483">'Razor'</span></span>
- <span data-ttu-id="6102e-484">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-486">'Blazor'</span></span>
- <span data-ttu-id="6102e-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-487">'Identity'</span></span>
- <span data-ttu-id="6102e-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-489">'Razor'</span></span>
- <span data-ttu-id="6102e-490">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-490">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1) | 응답을 생성 하거나 원본 서버에서 유효성을 검사 한 이후의 시간 (초)입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="6102e-492">| | [만료](https://tools.ietf.org/html/rfc7234#section-5.3) | 응답이 오래 된 것으로 간주 되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="6102e-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | 동작 설정에 대 한 HTTP/1.0 캐시와의 이전 버전과의 호환성을 위해 존재 `no-cache` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="6102e-494">헤더가 있으면 `Cache-Control` `Pragma` 헤더가 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="6102e-495">| | [다름](https://tools.ietf.org/html/rfc7231#section-7.1.4) | 캐시 된 응답의 `Vary` 원래 요청과 새 요청 모두에서 헤더 필드가 모두 일치 하지 않는 경우 캐시 된 응답을 보내지 않도록 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="6102e-496">HTTP 기반 캐싱은 요청 캐시 제어 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="6102e-497">[Cache-control 헤더에 대 한 HTTP 1.1 캐싱 지정](https://tools.ietf.org/html/rfc7234#section-5.2) 에는 클라이언트가 보낸 유효한 헤더를 인식 하기 위해 캐시가 필요 합니다 `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="6102e-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="6102e-498">클라이언트는 헤더 값을 사용 하 여 요청을 수행 하 `no-cache` 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="6102e-499">`Cache-Control`HTTP 캐싱의 목표를 고려 하는 경우 항상 클라이언트 요청 헤더를 파악 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="6102e-500">공식 사양에서 캐싱은 클라이언트, 프록시 및 서버 네트워크에서 요청을 충족 하는 대기 시간 및 네트워크 오버 헤드를 줄이기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="6102e-501">원본 서버에서 로드를 제어 하는 방법은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="6102e-502">미들웨어가 공식 캐싱 사양을 준수 하기 때문에 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하는 경우이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="6102e-503">[미들웨어에 대해 계획 된 향상 된 기능은](https://github.com/dotnet/AspNetCore/issues/2612) 캐시 된 응답을 제공 하도록 결정할 때 요청의 헤더를 무시 하도록 미들웨어를 구성할 수 있는 기회 `Cache-Control` 입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="6102e-504">계획 된 향상 된 기능을 통해 서버 부하를 보다 효과적으로 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="6102e-505">ASP.NET Core의 기타 캐싱 기술</span><span class="sxs-lookup"><span data-stu-id="6102e-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="6102e-506">메모리 내 캐싱</span><span class="sxs-lookup"><span data-stu-id="6102e-506">In-memory caching</span></span>

<span data-ttu-id="6102e-507">메모리 내 캐싱은 서버 메모리를 사용 하 여 캐시 된 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="6102e-508">이 유형의 캐싱은 단일 서버 또는 *고정 세션*을 사용 하는 여러 서버에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="6102e-509">고정 세션은 클라이언트의 요청이 처리를 위해 항상 동일한 서버로 라우팅되는 것을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="6102e-510">자세한 내용은 <xref:performance/caching/memory>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6102e-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="6102e-511">분산 캐시</span><span class="sxs-lookup"><span data-stu-id="6102e-511">Distributed Cache</span></span>

<span data-ttu-id="6102e-512">앱이 클라우드 또는 서버 팜에서 호스팅될 때 분산 캐시를 사용 하 여 메모리에 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="6102e-513">캐시는 요청을 처리 하는 서버에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="6102e-514">클라이언트에 대해 캐시 된 데이터를 사용할 수 있는 경우 클라이언트는 그룹의 모든 서버에서 처리 한 요청을 제출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="6102e-515">ASP.NET Core는 SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)및 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 분산 캐시와 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="6102e-516">자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6102e-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="6102e-517">캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="6102e-517">Cache Tag Helper</span></span>

<span data-ttu-id="6102e-518">캐시 태그 도우미를 사용 하 여 MVC 뷰 또는 페이지에서 콘텐츠를 캐시 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="6102e-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="6102e-519">캐시 태그 도우미는 메모리 내 캐싱을 사용 하 여 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="6102e-520">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6102e-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="6102e-521">분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="6102e-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="6102e-522">분산 된 Razor 캐시 태그 도우미를 사용 하 여 분산 클라우드 또는 웹 팜 시나리오의 MVC 뷰나 페이지에서 콘텐츠를 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="6102e-523">분산 캐시 태그 도우미는 SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)또는 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 를 사용 하 여 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="6102e-524">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6102e-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="6102e-525">ResponseCache 특성</span><span class="sxs-lookup"><span data-stu-id="6102e-525">ResponseCache attribute</span></span>

<span data-ttu-id="6102e-526">는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 응답 캐싱에 적절 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="6102e-527">인증 된 클라이언트에 대 한 정보가 포함 된 콘텐츠에 대해 캐싱을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="6102e-528">사용자의 id에 따라 변경 되지 않는 콘텐츠나 사용자의 로그인 여부에 따라 캐싱을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="6102e-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>지정 된 쿼리 키 목록 값에 따라 저장 된 응답을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="6102e-530">의 단일 값 `*` 이 제공 되 면 미들웨어는 모든 요청 쿼리 문자열 매개 변수의 응답을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="6102e-531">속성을 설정 하려면 [응답 캐싱 미들웨어](xref:performance/caching/middleware) 를 사용 하도록 설정 해야 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6102e-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="6102e-532">그렇지 않으면 런타임 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="6102e-533">속성에 해당 하는 HTTP 헤더가 없습니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6102e-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="6102e-534">속성은 응답 캐싱 미들웨어에 의해 처리 되는 HTTP 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="6102e-535">미들웨어가 캐시 된 응답을 제공 하려면 쿼리 문자열 및 쿼리 문자열 값이 이전 요청과 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="6102e-536">예를 들어 다음 표에 표시 된 요청 및 결과의 순서를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="6102e-537">요청</span><span class="sxs-lookup"><span data-stu-id="6102e-537">Request</span></span>                          | <span data-ttu-id="6102e-538">결과</span><span class="sxs-lookup"><span data-stu-id="6102e-538">Result</span></span>                    |
| ---
<span data-ttu-id="6102e-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-540">'Blazor'</span></span>
- <span data-ttu-id="6102e-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-541">'Identity'</span></span>
- <span data-ttu-id="6102e-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-543">'Razor'</span></span>
- <span data-ttu-id="6102e-544">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-546">'Blazor'</span></span>
- <span data-ttu-id="6102e-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-547">'Identity'</span></span>
- <span data-ttu-id="6102e-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-549">'Razor'</span></span>
- <span data-ttu-id="6102e-550">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-552">'Blazor'</span></span>
- <span data-ttu-id="6102e-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-553">'Identity'</span></span>
- <span data-ttu-id="6102e-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-555">'Razor'</span></span>
- <span data-ttu-id="6102e-556">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-558">'Blazor'</span></span>
- <span data-ttu-id="6102e-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-559">'Identity'</span></span>
- <span data-ttu-id="6102e-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-561">'Razor'</span></span>
- <span data-ttu-id="6102e-562">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-564">'Blazor'</span></span>
- <span data-ttu-id="6102e-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-565">'Identity'</span></span>
- <span data-ttu-id="6102e-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-567">'Razor'</span></span>
- <span data-ttu-id="6102e-568">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-570">'Blazor'</span></span>
- <span data-ttu-id="6102e-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-571">'Identity'</span></span>
- <span data-ttu-id="6102e-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-573">'Razor'</span></span>
- <span data-ttu-id="6102e-574">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-576">'Blazor'</span></span>
- <span data-ttu-id="6102e-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-577">'Identity'</span></span>
- <span data-ttu-id="6102e-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-579">'Razor'</span></span>
- <span data-ttu-id="6102e-580">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-582">'Blazor'</span></span>
- <span data-ttu-id="6102e-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-583">'Identity'</span></span>
- <span data-ttu-id="6102e-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-585">'Razor'</span></span>
- <span data-ttu-id="6102e-586">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-588">'Blazor'</span></span>
- <span data-ttu-id="6102e-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-589">'Identity'</span></span>
- <span data-ttu-id="6102e-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-591">'Razor'</span></span>
- <span data-ttu-id="6102e-592">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-594">'Blazor'</span></span>
- <span data-ttu-id="6102e-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-595">'Identity'</span></span>
- <span data-ttu-id="6102e-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-597">'Razor'</span></span>
- <span data-ttu-id="6102e-598">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-600">'Blazor'</span></span>
- <span data-ttu-id="6102e-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-601">'Identity'</span></span>
- <span data-ttu-id="6102e-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-603">'Razor'</span></span>
- <span data-ttu-id="6102e-604">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-606">'Blazor'</span></span>
- <span data-ttu-id="6102e-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-607">'Identity'</span></span>
- <span data-ttu-id="6102e-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-609">'Razor'</span></span>
- <span data-ttu-id="6102e-610">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-612">'Blazor'</span></span>
- <span data-ttu-id="6102e-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-613">'Identity'</span></span>
- <span data-ttu-id="6102e-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-615">'Razor'</span></span>
- <span data-ttu-id="6102e-616">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-618">'Blazor'</span></span>
- <span data-ttu-id="6102e-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-619">'Identity'</span></span>
- <span data-ttu-id="6102e-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-621">'Razor'</span></span>
- <span data-ttu-id="6102e-622">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-622">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-623">---------------- | ---제목: author: 설명: monikerRange: ms: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-624">'Blazor'</span></span>
- <span data-ttu-id="6102e-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-625">'Identity'</span></span>
- <span data-ttu-id="6102e-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-627">'Razor'</span></span>
- <span data-ttu-id="6102e-628">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-630">'Blazor'</span></span>
- <span data-ttu-id="6102e-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-631">'Identity'</span></span>
- <span data-ttu-id="6102e-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-633">'Razor'</span></span>
- <span data-ttu-id="6102e-634">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-636">'Blazor'</span></span>
- <span data-ttu-id="6102e-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-637">'Identity'</span></span>
- <span data-ttu-id="6102e-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-639">'Razor'</span></span>
- <span data-ttu-id="6102e-640">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-642">'Blazor'</span></span>
- <span data-ttu-id="6102e-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-643">'Identity'</span></span>
- <span data-ttu-id="6102e-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-645">'Razor'</span></span>
- <span data-ttu-id="6102e-646">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-648">'Blazor'</span></span>
- <span data-ttu-id="6102e-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-649">'Identity'</span></span>
- <span data-ttu-id="6102e-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-651">'Razor'</span></span>
- <span data-ttu-id="6102e-652">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-654">'Blazor'</span></span>
- <span data-ttu-id="6102e-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-655">'Identity'</span></span>
- <span data-ttu-id="6102e-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-657">'Razor'</span></span>
- <span data-ttu-id="6102e-658">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-660">'Blazor'</span></span>
- <span data-ttu-id="6102e-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-661">'Identity'</span></span>
- <span data-ttu-id="6102e-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-663">'Razor'</span></span>
- <span data-ttu-id="6102e-664">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-666">'Blazor'</span></span>
- <span data-ttu-id="6102e-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-667">'Identity'</span></span>
- <span data-ttu-id="6102e-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-669">'Razor'</span></span>
- <span data-ttu-id="6102e-670">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-672">'Blazor'</span></span>
- <span data-ttu-id="6102e-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-673">'Identity'</span></span>
- <span data-ttu-id="6102e-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-675">'Razor'</span></span>
- <span data-ttu-id="6102e-676">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6102e-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6102e-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="6102e-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6102e-678">'Blazor'</span></span>
- <span data-ttu-id="6102e-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6102e-679">'Identity'</span></span>
- <span data-ttu-id="6102e-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6102e-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="6102e-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6102e-681">'Razor'</span></span>
- <span data-ttu-id="6102e-682">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="6102e-682">'SignalR' uid:</span></span> 

<span data-ttu-id="6102e-683">------------- | | `http://example.com?key1=value1` | 서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="6102e-684">| | `http://example.com?key1=value1` | 미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="6102e-685">| | `http://example.com?key1=value2` | 서버에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="6102e-686">첫 번째 요청은 서버에서 반환 되 고 미들웨어에 캐시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="6102e-687">쿼리 문자열이 이전 요청과 일치 하기 때문에 두 번째 요청은 미들웨어에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="6102e-688">쿼리 문자열 값이 이전 요청과 일치 하지 않으므로 세 번째 요청이 미들웨어 캐시에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="6102e-689">는를 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 통해를 구성 하 고 만드는 데 사용 됩니다 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="6102e-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="6102e-690">는 `ResponseCacheFilter` 응답의 적절 한 HTTP 헤더 및 기능을 업데이트 하는 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="6102e-691">필터:</span><span class="sxs-lookup"><span data-stu-id="6102e-691">The filter:</span></span>

* <span data-ttu-id="6102e-692">, 및에 대 한 기존 헤더 `Vary` 를 제거 `Cache-Control` `Pragma` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="6102e-693">에 설정 된 속성을 기반으로 적절 한 헤더를 작성 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="6102e-694">가 설정 된 경우 응답 캐싱 HTTP 기능을 업데이트 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="6102e-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="6102e-695">날</span><span class="sxs-lookup"><span data-stu-id="6102e-695">Vary</span></span>

<span data-ttu-id="6102e-696">이 헤더는 속성이 설정 된 경우에만 기록 됩니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> .</span><span class="sxs-lookup"><span data-stu-id="6102e-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="6102e-697">속성 값으로 설정 된 속성 `Vary` 입니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="6102e-698">다음 샘플에서는 속성을 사용 합니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> .</span><span class="sxs-lookup"><span data-stu-id="6102e-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="6102e-699">샘플 앱을 사용 하 여 브라우저의 네트워크 도구를 사용 하 여 응답 헤더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="6102e-700">다음 응답 헤더는 Cache1 페이지 응답과 함께 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="6102e-701">NoStore 및 위치입니다. 없음</span><span class="sxs-lookup"><span data-stu-id="6102e-701">NoStore and Location.None</span></span>

<span data-ttu-id="6102e-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>는 대부분의 다른 속성을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="6102e-703">이 속성이로 설정 되 면 `true` `Cache-Control` 헤더가로 설정 됩니다 `no-store` .</span><span class="sxs-lookup"><span data-stu-id="6102e-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="6102e-704"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>가로 설정 된 `None` 경우</span><span class="sxs-lookup"><span data-stu-id="6102e-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="6102e-705">`Cache-Control`가 `no-store,no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="6102e-706">`Pragma`가 `no-cache`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="6102e-707"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>가이 `false` 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 가 `None` , `Cache-Control` 및 이면가 `Pragma` 로 설정 됩니다 `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="6102e-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="6102e-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>는 일반적으로 `true` 오류 페이지에 대해로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="6102e-709">샘플 앱의 Cache2 페이지는 클라이언트에 응답을 저장 하지 않도록 지시 하는 응답 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="6102e-710">샘플 앱은 다음 헤더를 포함 하는 Cache2 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="6102e-711">위치 및 기간</span><span class="sxs-lookup"><span data-stu-id="6102e-711">Location and Duration</span></span>

<span data-ttu-id="6102e-712">캐싱을 사용 하도록 설정 하려면를 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 양수 값으로 설정 하 고 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `Any` (기본값) 또는 중 하나 여야 합니다 `Client` .</span><span class="sxs-lookup"><span data-stu-id="6102e-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="6102e-713">프레임 워크는 `Cache-Control` 헤더를 위치 값으로 설정 하 고 그 뒤에 응답의을 설정 합니다 `max-age` .</span><span class="sxs-lookup"><span data-stu-id="6102e-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="6102e-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>및의 옵션 `Any` 은 `Client` `Cache-Control` 각각 및의 헤더 값으로 변환 `public` `private` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="6102e-715">[Nostore 및 Location. None](#nostore-and-locationnone) 섹션에서 설명한 대로 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 을로 설정 하면 `None` `Cache-Control` 및 헤더가 모두 `Pragma` 로 설정 `no-cache` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="6102e-716">`Location.Any`( `Cache-Control` 로 설정 `public` )은 *클라이언트 또는 중간 프록시가* [응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시할 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="6102e-717">`Location.Client`( `Cache-Control` 로 설정 `private` )는 *클라이언트만* 값을 캐시할 수 있음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="6102e-718">[응답 캐싱 미들웨어](xref:performance/caching/middleware)를 포함 하 여 값을 캐시 해야 하는 중간 캐시는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="6102e-719">캐시 컨트롤 헤더는 응답을 캐시 하는 방법 및 방법에 대 한 지침을 클라이언트 및 중간 프록시에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="6102e-720">클라이언트 및 프록시가 [HTTP 1.1 캐싱 사양을](https://tools.ietf.org/html/rfc7234)인식 한다는 보장이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="6102e-721">[응답 캐싱 미들웨어](xref:performance/caching/middleware) 는 항상 사양에 의해 배치 된 캐싱 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="6102e-722">다음 예제에서는 샘플 앱의 Cache3 페이지 모델 및 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 기본값을 설정 하 고 종료 하 여 생성 된 헤더를 보여 줍니다 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> .</span><span class="sxs-lookup"><span data-stu-id="6102e-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="6102e-723">샘플 앱은 다음 헤더를 포함 하는 Cache3 페이지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="6102e-724">캐시 프로필</span><span class="sxs-lookup"><span data-stu-id="6102e-724">Cache profiles</span></span>

<span data-ttu-id="6102e-725">여러 컨트롤러 작업 특성에 대 한 응답 캐시 설정을 복제 하는 대신에서 MVC/Pages를 설정할 때 캐시 프로필을 옵션으로 구성할 수 있습니다 Razor `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6102e-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6102e-726">참조 된 캐시 프로필에 있는 값은에서 기본값으로 사용 되며 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 특성에 지정 된 모든 속성에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="6102e-727">캐시 프로필을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-727">Set up a cache profile.</span></span> <span data-ttu-id="6102e-728">다음 예제에서는 샘플 앱의 30 초 캐시 프로필을 보여 줍니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6102e-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="6102e-729">샘플 앱의 Cache4 페이지 모델은 캐시 프로필을 참조 합니다 `Default30` .</span><span class="sxs-lookup"><span data-stu-id="6102e-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="6102e-730">는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 다음에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="6102e-731">페이지 처리기 (클래스): 처리기 메서드에 특성을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="6102e-732">MVC 컨트롤러 (클래스)</span><span class="sxs-lookup"><span data-stu-id="6102e-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="6102e-733">MVC 작업 (메서드): 메서드 수준 특성은 클래스 수준 특성에 지정 된 설정을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="6102e-734">캐시 프로필에의 한 Cache4 페이지 응답에 적용 되는 결과 헤더는 `Default30` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6102e-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="6102e-735">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6102e-735">Additional resources</span></span>

* [<span data-ttu-id="6102e-736">캐시에 응답 저장</span><span class="sxs-lookup"><span data-stu-id="6102e-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="6102e-737">Cache-control</span><span class="sxs-lookup"><span data-stu-id="6102e-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
