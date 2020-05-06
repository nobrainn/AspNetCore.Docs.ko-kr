---
title: ASP.NET Core의 응답 캐싱 미들웨어
author: rick-anderson
description: ASP.NET Core에서 응답 캐싱 미들웨어를 구성하고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/middleware
ms.openlocfilehash: 360b85d642ad3104d2662a38acd8c86e3c56f292
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775052"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="81981-103">ASP.NET Core의 응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="81981-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="81981-104">작성자: [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="81981-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81981-105">이 문서에서는 ASP.NET Core 앱에서 응답 캐싱 미들웨어를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="81981-106">미들웨어는 응답을 캐시할 시기를 결정 하 고, 응답을 저장 하 고, 캐시에서 응답을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="81981-107">HTTP 캐싱 및 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성에 대 한 소개는 [응답 캐싱](xref:performance/caching/response)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="81981-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81981-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="81981-109">구성</span><span class="sxs-lookup"><span data-stu-id="81981-109">Configuration</span></span>

<span data-ttu-id="81981-110">응답 캐싱 미들웨어는 공유 프레임 워크를 통해 ASP.NET Core 앱에 대해 암시적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="81981-111">에서 `Startup.ConfigureServices`응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="81981-112">의 `Startup.Configure`요청 처리 파이프라인에 미들웨어를 추가 하 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 는 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="81981-113">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="81981-114">[Cache-control은](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="81981-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; 는 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청의 경우와 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="81981-116">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="81981-117">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="81981-118">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="81981-119">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="81981-120">옵션</span><span class="sxs-lookup"><span data-stu-id="81981-120">Options</span></span>

<span data-ttu-id="81981-121">응답 캐싱 옵션은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="81981-122">옵션</span><span class="sxs-lookup"><span data-stu-id="81981-122">Option</span></span> | <span data-ttu-id="81981-123">설명</span><span class="sxs-lookup"><span data-stu-id="81981-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="81981-124">응답 본문에 대해 캐시할 수 있는 최대 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="81981-125">기본값은 `64 * 1024 * 1024` (64 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="81981-126">응답 캐시 미들웨어의 크기 제한 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="81981-127">기본값은 `100 * 1024 * 1024` (100 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="81981-128">대/소문자를 구분 하는 경로에서 응답이 캐시 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="81981-129">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-129">The default value is `false`.</span></span> |

<span data-ttu-id="81981-130">다음 예제에서는 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="81981-131">본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="81981-132">대/소문자 구분 경로를 기준으로 응답을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="81981-133">예를 들어 `/page1` 및 `/Page1` 은 별도로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="81981-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="81981-134">VaryByQueryKeys</span></span>

<span data-ttu-id="81981-135">MVC/web API 컨트롤러나 Razor Pages 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-135">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="81981-136">미들웨어 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>를 엄격히 필요로 하 `[ResponseCache]` 는 특성의 유일한 매개 변수는 실제 HTTP 헤더에 해당 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="81981-137">자세한 내용은 <xref:performance/caching/response#responsecache-attribute>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="81981-138">`[ResponseCache]` 특성을 사용 하지 않는 경우 응답 캐싱은에서 달라질 수 있습니다 `VaryByQueryKeys`.</span><span class="sxs-lookup"><span data-stu-id="81981-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="81981-139">다음과 같이 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="81981-140">`*` 에서 `VaryByQueryKeys` 와 같은 단일 값을 사용 하는 경우 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="81981-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="81981-141">응답 캐싱 미들웨어에서 사용 하는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="81981-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="81981-142">다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="81981-143">헤더</span><span class="sxs-lookup"><span data-stu-id="81981-143">Header</span></span> | <span data-ttu-id="81981-144">설명</span><span class="sxs-lookup"><span data-stu-id="81981-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="81981-145">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="81981-146">미들웨어는 `public` cache 지시문으로 표시 된 캐싱 응답만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="81981-147">다음 매개 변수를 사용 하 여 캐싱 제어:</span><span class="sxs-lookup"><span data-stu-id="81981-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="81981-148">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="81981-148">max-age</span></span></li><li><span data-ttu-id="81981-149">최대-오래 된&#8224;</span><span class="sxs-lookup"><span data-stu-id="81981-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="81981-150">최소-새로</span><span class="sxs-lookup"><span data-stu-id="81981-150">min-fresh</span></span></li><li><span data-ttu-id="81981-151">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="81981-151">must-revalidate</span></span></li><li><span data-ttu-id="81981-152">no-cache</span><span class="sxs-lookup"><span data-stu-id="81981-152">no-cache</span></span></li><li><span data-ttu-id="81981-153">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="81981-153">no-store</span></span></li><li><span data-ttu-id="81981-154">-인 경우에만 캐시</span><span class="sxs-lookup"><span data-stu-id="81981-154">only-if-cached</span></span></li><li><span data-ttu-id="81981-155">private</span><span class="sxs-lookup"><span data-stu-id="81981-155">private</span></span></li><li><span data-ttu-id="81981-156">public</span><span class="sxs-lookup"><span data-stu-id="81981-156">public</span></span></li><li><span data-ttu-id="81981-157">s-maxage</span><span class="sxs-lookup"><span data-stu-id="81981-157">s-maxage</span></span></li><li><span data-ttu-id="81981-158">프록시-유효성 검사&#8225;</span><span class="sxs-lookup"><span data-stu-id="81981-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="81981-159">&#8224;에 대 `max-stale`한 제한이 지정 되지 않은 경우 미들웨어는 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="81981-160">&#8225;`proxy-revalidate` 는와 `must-revalidate`동일한 효과가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="81981-161">자세한 내용은 [RFC 7231: 요청 캐시-제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="81981-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="81981-162">요청의 `Pragma: no-cache` 헤더는와 `Cache-Control: no-cache`동일한 결과를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="81981-163">이 헤더는 `Cache-Control` 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="81981-164">HTTP/1.0과의 이전 버전과의 호환성을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="81981-165">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="81981-166">하나 이상의 쿠키를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="81981-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="81981-167">`Vary` 헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="81981-168">예를 들어 `Vary: Accept-Encoding` 헤더를 포함 하 여 인코딩을 통해 응답을 캐시 하 `Accept-Encoding: gzip` 고 `Accept-Encoding: text/plain` 헤더를 포함 하는 요청에 대 한 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="81981-169">헤더 값 `*` 이 인 응답은 저장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="81981-170">이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 `Cache-Control` 헤더로 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="81981-171">값이이 `*` 고 응답 `ETag` 의이 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="81981-172">그렇지 않으면 304 (수정 되지 않음) 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="81981-173">`If-None-Match` 헤더가 없으면 캐시 된 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="81981-174">그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="81981-175">캐시에서 서비스를 제공 하 `Date` 는 경우 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="81981-176">캐시에서 서비스를 제공 하 `Content-Length` 는 경우 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="81981-177">원래 `Age` 응답에서 보낸 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="81981-178">미들웨어는 캐시 된 응답을 제공할 때 새 값을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="81981-179">캐싱 측면에서 요청 Cache-control 지시문</span><span class="sxs-lookup"><span data-stu-id="81981-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="81981-180">미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="81981-181">규칙에는 클라이언트가 보낸 유효한 `Cache-Control` 헤더를 인식 하기 위해 캐시가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="81981-182">사양에 따라 클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="81981-183">현재 미들웨어는 공식 캐싱 사양을 준수 하므로 미들웨어를 사용할 때이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="81981-184">캐싱 동작을 보다 세부적으로 제어 하려면 ASP.NET Core의 다른 캐싱 기능을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="81981-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="81981-185">다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="81981-186">문제 해결</span><span class="sxs-lookup"><span data-stu-id="81981-186">Troubleshooting</span></span>

<span data-ttu-id="81981-187">캐싱 동작이 예상과 다른 경우 응답이 캐시 가능 하 고 캐시에서 제공 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="81981-188">요청의 들어오는 헤더와 응답의 나가는 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="81981-189">디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="81981-190">캐싱 동작을 테스트 하 고 문제를 해결할 때 브라우저는 원치 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="81981-191">예를 들어, 브라우저에서 페이지를 `Cache-Control` 새로 고칠 `no-cache` 때 `max-age=0` 헤더를 또는로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="81981-192">다음 도구는 명시적으로 요청 헤더를 설정할 수 있으며 캐싱을 테스트 하는 데 기본 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="81981-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="81981-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="81981-194">Postman</span><span class="sxs-lookup"><span data-stu-id="81981-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="81981-195">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="81981-195">Conditions for caching</span></span>

* <span data-ttu-id="81981-196">요청은 200 (OK) 상태 코드를 포함 하는 서버 응답을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="81981-197">요청 메서드는 GET 또는 HEAD 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="81981-198">에서 `Startup.Configure`캐싱은 캐싱을 필요로 하는 미들웨어 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="81981-199">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="81981-200">`Authorization` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="81981-201">`Cache-Control`헤더 매개 변수는 유효 해야 하 고 응답은 표시 되 `public` 고 표시 `private`되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="81981-202">헤더가 있을 때 `Cache-Control` 헤더가 `Pragma` 헤더를 재정의 하므로 헤더가 없으면 헤더가 없어야 합니다. `Pragma: no-cache` `Cache-Control`</span><span class="sxs-lookup"><span data-stu-id="81981-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="81981-203">`Set-Cookie` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="81981-204">`Vary`헤더 매개 변수는 유효 해야 하 `*`고와 같지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="81981-205">헤더 `Content-Length` 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="81981-206">는 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="81981-207">`Expires` 헤더와 `max-age` 및 `s-maxage` cache 지시문에 지정 된 대로 응답이 유효 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="81981-208">응답 버퍼링이 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-208">Response buffering must be successful.</span></span> <span data-ttu-id="81981-209">응답의 크기는 구성 된 또는 기본값 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="81981-210">응답의 본문 크기는 구성 된 또는 기본값 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="81981-211">[RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="81981-212">예를 들어 지시문 `no-store` 은 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="81981-213">자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="81981-214">CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 보안 토큰을 생성 하는 위조 방지 시스템 `Cache-Control` 은 `Pragma` 응답이 캐시 `no-cache` 되지 않도록 및 헤더를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="81981-215">HTML 양식 요소의 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>대 한 자세한 내용은을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="81981-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81981-216">추가 자료</span><span class="sxs-lookup"><span data-stu-id="81981-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81981-217">이 문서에서는 ASP.NET Core 앱에서 응답 캐싱 미들웨어를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-217">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="81981-218">미들웨어는 응답을 캐시할 시기를 결정 하 고, 응답을 저장 하 고, 캐시에서 응답을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-218">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="81981-219">HTTP 캐싱 및 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성에 대 한 소개는 [응답 캐싱](xref:performance/caching/response)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-219">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="81981-220">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81981-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="81981-221">구성</span><span class="sxs-lookup"><span data-stu-id="81981-221">Configuration</span></span>

<span data-ttu-id="81981-222">[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 사용 하거나 [ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-222">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="81981-223">에서 `Startup.ConfigureServices`응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-223">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="81981-224">의 `Startup.Configure`요청 처리 파이프라인에 미들웨어를 추가 하 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 는 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-224">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="81981-225">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-225">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="81981-226">[Cache-control은](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-226">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="81981-227">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; 는 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청의 경우와 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-227">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="81981-228">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-228">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="81981-229">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-229">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="81981-230">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-230">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="81981-231">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-231">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="81981-232">옵션</span><span class="sxs-lookup"><span data-stu-id="81981-232">Options</span></span>

<span data-ttu-id="81981-233">응답 캐싱 옵션은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-233">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="81981-234">옵션</span><span class="sxs-lookup"><span data-stu-id="81981-234">Option</span></span> | <span data-ttu-id="81981-235">설명</span><span class="sxs-lookup"><span data-stu-id="81981-235">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="81981-236">응답 본문에 대해 캐시할 수 있는 최대 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-236">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="81981-237">기본값은 `64 * 1024 * 1024` (64 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-237">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="81981-238">응답 캐시 미들웨어의 크기 제한 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-238">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="81981-239">기본값은 `100 * 1024 * 1024` (100 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-239">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="81981-240">대/소문자를 구분 하는 경로에서 응답이 캐시 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-240">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="81981-241">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="81981-241">The default value is `false`.</span></span> |

<span data-ttu-id="81981-242">다음 예제에서는 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-242">The following example configures the middleware to:</span></span>

* <span data-ttu-id="81981-243">본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-243">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="81981-244">대/소문자 구분 경로를 기준으로 응답을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-244">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="81981-245">예를 들어 `/page1` 및 `/Page1` 은 별도로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-245">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="81981-246">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="81981-246">VaryByQueryKeys</span></span>

<span data-ttu-id="81981-247">MVC/web API 컨트롤러나 Razor Pages 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-247">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="81981-248">미들웨어 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>를 엄격히 필요로 하 `[ResponseCache]` 는 특성의 유일한 매개 변수는 실제 HTTP 헤더에 해당 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-248">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="81981-249">자세한 내용은 <xref:performance/caching/response#responsecache-attribute>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-249">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="81981-250">`[ResponseCache]` 특성을 사용 하지 않는 경우 응답 캐싱은에서 달라질 수 있습니다 `VaryByQueryKeys`.</span><span class="sxs-lookup"><span data-stu-id="81981-250">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="81981-251">다음과 같이 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-251">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="81981-252">`*` 에서 `VaryByQueryKeys` 와 같은 단일 값을 사용 하는 경우 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="81981-252">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="81981-253">응답 캐싱 미들웨어에서 사용 하는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="81981-253">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="81981-254">다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-254">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="81981-255">헤더</span><span class="sxs-lookup"><span data-stu-id="81981-255">Header</span></span> | <span data-ttu-id="81981-256">설명</span><span class="sxs-lookup"><span data-stu-id="81981-256">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="81981-257">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-257">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="81981-258">미들웨어는 `public` cache 지시문으로 표시 된 캐싱 응답만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-258">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="81981-259">다음 매개 변수를 사용 하 여 캐싱 제어:</span><span class="sxs-lookup"><span data-stu-id="81981-259">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="81981-260">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="81981-260">max-age</span></span></li><li><span data-ttu-id="81981-261">최대-오래 된&#8224;</span><span class="sxs-lookup"><span data-stu-id="81981-261">max-stale&#8224;</span></span></li><li><span data-ttu-id="81981-262">최소-새로</span><span class="sxs-lookup"><span data-stu-id="81981-262">min-fresh</span></span></li><li><span data-ttu-id="81981-263">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="81981-263">must-revalidate</span></span></li><li><span data-ttu-id="81981-264">no-cache</span><span class="sxs-lookup"><span data-stu-id="81981-264">no-cache</span></span></li><li><span data-ttu-id="81981-265">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="81981-265">no-store</span></span></li><li><span data-ttu-id="81981-266">-인 경우에만 캐시</span><span class="sxs-lookup"><span data-stu-id="81981-266">only-if-cached</span></span></li><li><span data-ttu-id="81981-267">private</span><span class="sxs-lookup"><span data-stu-id="81981-267">private</span></span></li><li><span data-ttu-id="81981-268">public</span><span class="sxs-lookup"><span data-stu-id="81981-268">public</span></span></li><li><span data-ttu-id="81981-269">s-maxage</span><span class="sxs-lookup"><span data-stu-id="81981-269">s-maxage</span></span></li><li><span data-ttu-id="81981-270">프록시-유효성 검사&#8225;</span><span class="sxs-lookup"><span data-stu-id="81981-270">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="81981-271">&#8224;에 대 `max-stale`한 제한이 지정 되지 않은 경우 미들웨어는 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-271">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="81981-272">&#8225;`proxy-revalidate` 는와 `must-revalidate`동일한 효과가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-272">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="81981-273">자세한 내용은 [RFC 7231: 요청 캐시-제어 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="81981-273">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="81981-274">요청의 `Pragma: no-cache` 헤더는와 `Cache-Control: no-cache`동일한 결과를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-274">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="81981-275">이 헤더는 `Cache-Control` 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-275">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="81981-276">HTTP/1.0과의 이전 버전과의 호환성을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-276">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="81981-277">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-277">The response isn't cached if the header exists.</span></span> <span data-ttu-id="81981-278">하나 이상의 쿠키를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [쿠키 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="81981-278">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="81981-279">`Vary` 헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-279">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="81981-280">예를 들어 `Vary: Accept-Encoding` 헤더를 포함 하 여 인코딩을 통해 응답을 캐시 하 `Accept-Encoding: gzip` 고 `Accept-Encoding: text/plain` 헤더를 포함 하는 요청에 대 한 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-280">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="81981-281">헤더 값 `*` 이 인 응답은 저장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-281">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="81981-282">이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 `Cache-Control` 헤더로 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-282">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="81981-283">값이이 `*` 고 응답 `ETag` 의이 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-283">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="81981-284">그렇지 않으면 304 (수정 되지 않음) 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-284">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="81981-285">`If-None-Match` 헤더가 없으면 캐시 된 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-285">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="81981-286">그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-286">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="81981-287">캐시에서 서비스를 제공 하 `Date` 는 경우 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-287">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="81981-288">캐시에서 서비스를 제공 하 `Content-Length` 는 경우 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-288">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="81981-289">원래 `Age` 응답에서 보낸 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-289">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="81981-290">미들웨어는 캐시 된 응답을 제공할 때 새 값을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-290">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="81981-291">캐싱 측면에서 요청 Cache-control 지시문</span><span class="sxs-lookup"><span data-stu-id="81981-291">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="81981-292">미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-292">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="81981-293">규칙에는 클라이언트가 보낸 유효한 `Cache-Control` 헤더를 인식 하기 위해 캐시가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-293">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="81981-294">사양에 따라 클라이언트는 `no-cache` 헤더 값을 사용 하 여 요청을 수행 하 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-294">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="81981-295">현재 미들웨어는 공식 캐싱 사양을 준수 하므로 미들웨어를 사용할 때이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-295">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="81981-296">캐싱 동작을 보다 세부적으로 제어 하려면 ASP.NET Core의 다른 캐싱 기능을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="81981-296">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="81981-297">다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-297">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="81981-298">문제 해결</span><span class="sxs-lookup"><span data-stu-id="81981-298">Troubleshooting</span></span>

<span data-ttu-id="81981-299">캐싱 동작이 예상과 다른 경우 응답이 캐시 가능 하 고 캐시에서 제공 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-299">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="81981-300">요청의 들어오는 헤더와 응답의 나가는 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-300">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="81981-301">디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-301">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="81981-302">캐싱 동작을 테스트 하 고 문제를 해결할 때 브라우저는 원치 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-302">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="81981-303">예를 들어, 브라우저에서 페이지를 `Cache-Control` 새로 고칠 `no-cache` 때 `max-age=0` 헤더를 또는로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-303">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="81981-304">다음 도구는 명시적으로 요청 헤더를 설정할 수 있으며 캐싱을 테스트 하는 데 기본 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="81981-304">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="81981-305">Fiddler</span><span class="sxs-lookup"><span data-stu-id="81981-305">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="81981-306">Postman</span><span class="sxs-lookup"><span data-stu-id="81981-306">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="81981-307">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="81981-307">Conditions for caching</span></span>

* <span data-ttu-id="81981-308">요청은 200 (OK) 상태 코드를 포함 하는 서버 응답을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-308">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="81981-309">요청 메서드는 GET 또는 HEAD 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-309">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="81981-310">에서 `Startup.Configure`캐싱은 캐싱을 필요로 하는 미들웨어 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-310">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="81981-311">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-311">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="81981-312">`Authorization` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-312">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="81981-313">`Cache-Control`헤더 매개 변수는 유효 해야 하 고 응답은 표시 되 `public` 고 표시 `private`되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-313">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="81981-314">헤더가 있을 때 `Cache-Control` 헤더가 `Pragma` 헤더를 재정의 하므로 헤더가 없으면 헤더가 없어야 합니다. `Pragma: no-cache` `Cache-Control`</span><span class="sxs-lookup"><span data-stu-id="81981-314">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="81981-315">`Set-Cookie` 헤더가 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-315">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="81981-316">`Vary`헤더 매개 변수는 유효 해야 하 `*`고와 같지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-316">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="81981-317">헤더 `Content-Length` 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-317">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="81981-318">는 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="81981-318">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="81981-319">`Expires` 헤더와 `max-age` 및 `s-maxage` cache 지시문에 지정 된 대로 응답이 유효 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-319">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="81981-320">응답 버퍼링이 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-320">Response buffering must be successful.</span></span> <span data-ttu-id="81981-321">응답의 크기는 구성 된 또는 기본값 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-321">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="81981-322">응답의 본문 크기는 구성 된 또는 기본값 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-322">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="81981-323">[RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-323">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="81981-324">예를 들어 지시문 `no-store` 은 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-324">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="81981-325">자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="81981-325">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="81981-326">CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 보안 토큰을 생성 하는 위조 방지 시스템 `Cache-Control` 은 `Pragma` 응답이 캐시 `no-cache` 되지 않도록 및 헤더를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="81981-326">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="81981-327">HTML 양식 요소의 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>대 한 자세한 내용은을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="81981-327">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81981-328">추가 자료</span><span class="sxs-lookup"><span data-stu-id="81981-328">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
