---
<span data-ttu-id="5a077-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-102">'Blazor'</span></span>
- <span data-ttu-id="5a077-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-103">'Identity'</span></span>
- <span data-ttu-id="5a077-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-105">'Razor'</span></span>
- <span data-ttu-id="5a077-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="5a077-107">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="5a077-108">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="5a077-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a077-109">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="5a077-110">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="5a077-111">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="5a077-112">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="5a077-113">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="5a077-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="5a077-114">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="5a077-114">Forwarded headers</span></span>

<span data-ttu-id="5a077-115">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="5a077-116">헤더</span><span class="sxs-lookup"><span data-stu-id="5a077-116">Header</span></span> | <span data-ttu-id="5a077-117">설명</span><span class="sxs-lookup"><span data-stu-id="5a077-117">Description</span></span> |
| ---
<span data-ttu-id="5a077-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-119">'Blazor'</span></span>
- <span data-ttu-id="5a077-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-120">'Identity'</span></span>
- <span data-ttu-id="5a077-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-122">'Razor'</span></span>
- <span data-ttu-id="5a077-123">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-123">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-125">'Blazor'</span></span>
- <span data-ttu-id="5a077-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-126">'Identity'</span></span>
- <span data-ttu-id="5a077-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-128">'Razor'</span></span>
- <span data-ttu-id="5a077-129">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-131">'Blazor'</span></span>
- <span data-ttu-id="5a077-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-132">'Identity'</span></span>
- <span data-ttu-id="5a077-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-134">'Razor'</span></span>
- <span data-ttu-id="5a077-135">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-137">'Blazor'</span></span>
- <span data-ttu-id="5a077-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-138">'Identity'</span></span>
- <span data-ttu-id="5a077-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-140">'Razor'</span></span>
- <span data-ttu-id="5a077-141">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-141">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-142">------ | | X-Forwarded-For | 요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="5a077-143">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="5a077-144">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="5a077-145">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="5a077-146">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="5a077-147">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="5a077-148">| | X-Forwarded-Proto | 원래 체계의 값입니다(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5a077-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="5a077-149">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="5a077-150">| | X-Forwarded-Host | 호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="5a077-151">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="5a077-152">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="5a077-153">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="5a077-154">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-154">The middleware updates:</span></span>

* <span data-ttu-id="5a077-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="5a077-156">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="5a077-157">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="5a077-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="5a077-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="5a077-160">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="5a077-161">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-161">The default settings are:</span></span>

* <span data-ttu-id="5a077-162">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="5a077-163">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="5a077-164">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="5a077-165">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="5a077-166">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="5a077-167">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="5a077-168">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="5a077-169">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="5a077-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="5a077-170">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="5a077-171">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="5a077-172">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="5a077-173">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5a077-174">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="5a077-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5a077-175">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="5a077-176">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="5a077-177">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="5a077-178"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a077-179">다른 미들웨어를 호출하기 전에 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="5a077-180">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5a077-181">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="5a077-182">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-182">Nginx configuration</span></span>

<span data-ttu-id="5a077-183">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="5a077-184">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="5a077-185">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-185">Apache configuration</span></span>

<span data-ttu-id="5a077-186">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="5a077-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="5a077-187">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="5a077-188">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="5a077-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="5a077-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="5a077-190">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-190">The following example changes the default values:</span></span>

* <span data-ttu-id="5a077-191">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="5a077-192">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="5a077-193">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="5a077-194">옵션</span><span class="sxs-lookup"><span data-stu-id="5a077-194">Option</span></span> | <span data-ttu-id="5a077-195">설명</span><span class="sxs-lookup"><span data-stu-id="5a077-195">Description</span></span> |
| ---
<span data-ttu-id="5a077-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-197">'Blazor'</span></span>
- <span data-ttu-id="5a077-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-198">'Identity'</span></span>
- <span data-ttu-id="5a077-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-200">'Razor'</span></span>
- <span data-ttu-id="5a077-201">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-201">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-203">'Blazor'</span></span>
- <span data-ttu-id="5a077-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-204">'Identity'</span></span>
- <span data-ttu-id="5a077-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-206">'Razor'</span></span>
- <span data-ttu-id="5a077-207">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-209">'Blazor'</span></span>
- <span data-ttu-id="5a077-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-210">'Identity'</span></span>
- <span data-ttu-id="5a077-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-212">'Razor'</span></span>
- <span data-ttu-id="5a077-213">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-215">'Blazor'</span></span>
- <span data-ttu-id="5a077-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-216">'Identity'</span></span>
- <span data-ttu-id="5a077-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-218">'Razor'</span></span>
- <span data-ttu-id="5a077-219">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-219">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | `X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="5a077-221">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="5a077-222">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="5a077-223">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="5a077-224">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="5a077-225">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="5a077-226">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="5a077-227">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="5a077-228">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="5a077-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="5a077-229">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="5a077-230">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="5a077-231">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="5a077-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="5a077-233">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-234">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="5a077-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | 처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="5a077-236">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="5a077-237">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="5a077-238">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5a077-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="5a077-240">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-241">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="5a077-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="5a077-243">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-244">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="5a077-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | 처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="5a077-246">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="5a077-247">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="5a077-248">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5a077-249">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="5a077-250">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-250">The default is `1`.</span></span> <span data-ttu-id="5a077-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | 전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="5a077-252">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="5a077-253">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5a077-254">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-255">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5a077-256">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5a077-257">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="5a077-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | 전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="5a077-259">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="5a077-260">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5a077-261">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-262">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5a077-263">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5a077-264">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="5a077-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="5a077-266">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="5a077-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="5a077-268">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="5a077-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="5a077-270">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="5a077-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | 처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="5a077-272">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="5a077-273">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="5a077-274">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="5a077-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="5a077-275">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="5a077-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="5a077-276">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="5a077-277">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="5a077-278">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="5a077-279">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="5a077-280">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="5a077-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="5a077-282">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="5a077-283">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="5a077-284">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5a077-285">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="5a077-286">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="5a077-287">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="5a077-288">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="5a077-289">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="5a077-290">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="5a077-291">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-292">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="5a077-293">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="5a077-294">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="5a077-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="5a077-295">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="5a077-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="5a077-296">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="5a077-296">Converted prefix length: 104</span></span>

<span data-ttu-id="5a077-297">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="5a077-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="5a077-298">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="5a077-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="5a077-299">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="5a077-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="5a077-300"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="5a077-301">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="5a077-302">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="5a077-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="5a077-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="5a077-304">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="5a077-305">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-305">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="5a077-306">인증서 전달</span><span class="sxs-lookup"><span data-stu-id="5a077-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="5a077-307">Azure</span><span class="sxs-lookup"><span data-stu-id="5a077-307">Azure</span></span>

<span data-ttu-id="5a077-308">인증서 전달에 대한 Azure App Service를 구성하려면 [Azure App Service에 대한 TLS 상호 인증 구성](/azure/app-service/app-service-web-configure-tls-mutual-auth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="5a077-309">다음 지침은 ASP.NET Core 앱 구성과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="5a077-310">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="5a077-311">인증서 전달 미들웨어를 구성하여 Azure를 사용하는 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="5a077-312">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="5a077-313">다른 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="5a077-313">Other web proxies</span></span>

<span data-ttu-id="5a077-314">IIS 또는 Azure App Service의 ARR(애플리케이션 요청 라우팅)이 아닌 프록시를 사용하는 경우 HTTP 헤더에서 받은 인증서를 전달하도록 프록시를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="5a077-315">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="5a077-316">인증서 전달 미들웨어를 구성하여 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="5a077-317">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="5a077-318">프록시가 Nginx에서와 같이 인증서를 base64 인코딩하지 않는 경우 `HeaderConverter` 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="5a077-319">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="5a077-320">문제 해결</span><span class="sxs-lookup"><span data-stu-id="5a077-320">Troubleshoot</span></span>

<span data-ttu-id="5a077-321">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5a077-322">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="5a077-323">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="5a077-324">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="5a077-325">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="5a077-326">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="5a077-327">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="5a077-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="5a077-328">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="5a077-329">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="5a077-330">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="5a077-331">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5a077-332">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="5a077-333">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="5a077-334">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="5a077-335">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="5a077-336">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="5a077-337">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="5a077-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a077-338">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="5a077-339">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="5a077-340">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a077-341">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5a077-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="5a077-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="5a077-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a077-343">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="5a077-344">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="5a077-345">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="5a077-346">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="5a077-347">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="5a077-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="5a077-348">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="5a077-348">Forwarded headers</span></span>

<span data-ttu-id="5a077-349">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="5a077-350">헤더</span><span class="sxs-lookup"><span data-stu-id="5a077-350">Header</span></span> | <span data-ttu-id="5a077-351">설명</span><span class="sxs-lookup"><span data-stu-id="5a077-351">Description</span></span> |
| ---
<span data-ttu-id="5a077-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-353">'Blazor'</span></span>
- <span data-ttu-id="5a077-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-354">'Identity'</span></span>
- <span data-ttu-id="5a077-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-356">'Razor'</span></span>
- <span data-ttu-id="5a077-357">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-357">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-359">'Blazor'</span></span>
- <span data-ttu-id="5a077-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-360">'Identity'</span></span>
- <span data-ttu-id="5a077-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-362">'Razor'</span></span>
- <span data-ttu-id="5a077-363">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-365">'Blazor'</span></span>
- <span data-ttu-id="5a077-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-366">'Identity'</span></span>
- <span data-ttu-id="5a077-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-368">'Razor'</span></span>
- <span data-ttu-id="5a077-369">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-371">'Blazor'</span></span>
- <span data-ttu-id="5a077-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-372">'Identity'</span></span>
- <span data-ttu-id="5a077-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-374">'Razor'</span></span>
- <span data-ttu-id="5a077-375">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-375">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-376">------ | | X-Forwarded-For | 요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="5a077-377">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="5a077-378">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="5a077-379">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="5a077-380">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="5a077-381">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="5a077-382">| | X-Forwarded-Proto | 원래 체계의 값입니다(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="5a077-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="5a077-383">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="5a077-384">| | X-Forwarded-Host | 호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="5a077-385">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="5a077-386">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="5a077-387">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="5a077-388">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-388">The middleware updates:</span></span>

* <span data-ttu-id="5a077-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="5a077-390">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="5a077-391">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="5a077-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="5a077-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="5a077-394">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="5a077-395">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-395">The default settings are:</span></span>

* <span data-ttu-id="5a077-396">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="5a077-397">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="5a077-398">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="5a077-399">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="5a077-400">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="5a077-401">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="5a077-402">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="5a077-403">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="5a077-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="5a077-404">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="5a077-405">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="5a077-406">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="5a077-407">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="5a077-408">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="5a077-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="5a077-409">[Out of Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="5a077-410">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="5a077-411">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="5a077-412"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `Startup.ConfigureServices`의 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a077-413">다른 미들웨어를 호출하기 전에 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="5a077-414">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5a077-415">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="5a077-416">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-416">Nginx configuration</span></span>

<span data-ttu-id="5a077-417">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="5a077-418">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="5a077-419">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-419">Apache configuration</span></span>

<span data-ttu-id="5a077-420">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="5a077-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="5a077-421">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="5a077-422">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="5a077-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="5a077-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="5a077-424">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-424">The following example changes the default values:</span></span>

* <span data-ttu-id="5a077-425">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="5a077-426">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="5a077-427">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="5a077-428">옵션</span><span class="sxs-lookup"><span data-stu-id="5a077-428">Option</span></span> | <span data-ttu-id="5a077-429">설명</span><span class="sxs-lookup"><span data-stu-id="5a077-429">Description</span></span> |
| ---
<span data-ttu-id="5a077-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-431">'Blazor'</span></span>
- <span data-ttu-id="5a077-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-432">'Identity'</span></span>
- <span data-ttu-id="5a077-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-434">'Razor'</span></span>
- <span data-ttu-id="5a077-435">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-435">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-437">'Blazor'</span></span>
- <span data-ttu-id="5a077-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-438">'Identity'</span></span>
- <span data-ttu-id="5a077-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-440">'Razor'</span></span>
- <span data-ttu-id="5a077-441">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-443">'Blazor'</span></span>
- <span data-ttu-id="5a077-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-444">'Identity'</span></span>
- <span data-ttu-id="5a077-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-446">'Razor'</span></span>
- <span data-ttu-id="5a077-447">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a077-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a077-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a077-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a077-449">'Blazor'</span></span>
- <span data-ttu-id="5a077-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a077-450">'Identity'</span></span>
- <span data-ttu-id="5a077-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a077-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a077-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a077-452">'Razor'</span></span>
- <span data-ttu-id="5a077-453">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5a077-453">'SignalR' uid:</span></span> 

<span data-ttu-id="5a077-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | `X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="5a077-455">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="5a077-456">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="5a077-457">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="5a077-458">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="5a077-459">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="5a077-460">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="5a077-461">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="5a077-462">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="5a077-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="5a077-463">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="5a077-464">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="5a077-465">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="5a077-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="5a077-467">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-468">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="5a077-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | 처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="5a077-470">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="5a077-471">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="5a077-472">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="5a077-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="5a077-474">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-475">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="5a077-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="5a077-477">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="5a077-478">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="5a077-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | 처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="5a077-480">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="5a077-481">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="5a077-482">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5a077-483">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="5a077-484">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-484">The default is `1`.</span></span> <span data-ttu-id="5a077-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | 전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="5a077-486">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="5a077-487">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5a077-488">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-489">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5a077-490">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5a077-491">기본값은 `IPAddress.Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="5a077-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | 전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="5a077-493">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="5a077-494">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="5a077-495">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-496">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="5a077-497">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="5a077-498">기본값은 `IPAddress.IPv6Loopback`에 대한 단일 항목을 포함하는 `IList`\<<xref:System.Net.IPAddress>>입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="5a077-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="5a077-500">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="5a077-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="5a077-502">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="5a077-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="5a077-504">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="5a077-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | 처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="5a077-506">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="5a077-507">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="5a077-508">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="5a077-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="5a077-509">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="5a077-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="5a077-510">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="5a077-511">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="5a077-512">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="5a077-513">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="5a077-514">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="5a077-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="5a077-516">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="5a077-517">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="5a077-518">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5a077-519">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="5a077-520">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="5a077-521">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="5a077-522">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="5a077-523">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="5a077-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="5a077-524">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="5a077-525">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="5a077-526">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="5a077-527">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="5a077-528">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="5a077-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="5a077-529">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="5a077-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="5a077-530">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="5a077-530">Converted prefix length: 104</span></span>

<span data-ttu-id="5a077-531">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="5a077-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="5a077-532">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="5a077-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="5a077-533">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="5a077-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="5a077-534"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="5a077-535">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="5a077-536">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="5a077-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="5a077-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="5a077-538">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="5a077-539">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-539">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="5a077-540">문제 해결</span><span class="sxs-lookup"><span data-stu-id="5a077-540">Troubleshoot</span></span>

<span data-ttu-id="5a077-541">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5a077-542">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="5a077-543">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="5a077-544">앱 응답에 헤더를 기록하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="5a077-545">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="5a077-546">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="5a077-547">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="5a077-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="5a077-548">[시작 시 로그 만들기](xref:fundamentals/logging/index#create-logs-in-startup)에 설명된 대로 `ILogger<Startup>`을 `Startup` 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="5a077-549">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="5a077-550">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="5a077-551">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="5a077-552">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="5a077-553">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="5a077-554">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="5a077-555">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="5a077-556">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="5a077-557">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `Startup.ConfigureServices`의 `KnownProxies`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `KnownNetworks`에 추가).</span><span class="sxs-lookup"><span data-stu-id="5a077-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a077-558">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a077-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="5a077-559">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="5a077-560">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="5a077-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a077-561">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5a077-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="5a077-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="5a077-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
