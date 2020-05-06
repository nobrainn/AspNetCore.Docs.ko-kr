---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773813"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="a61d1-103">ASP.NET Core에서 CORS (원본 간 요청) 사용</span><span class="sxs-lookup"><span data-stu-id="a61d1-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a61d1-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a61d1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a61d1-105">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a61d1-106">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a61d1-107">이러한 제한 사항을 *동일 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a61d1-108">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a61d1-109">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 수행할 수 있도록 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="a61d1-110">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a61d1-111">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="a61d1-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a61d1-112">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a61d1-113">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a61d1-114">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a61d1-115">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a61d1-116">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a61d1-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a61d1-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a61d1-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a61d1-119">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="a61d1-119">Same origin</span></span>

<span data-ttu-id="a61d1-120">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a61d1-121">이러한 두 Url의 출처는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a61d1-122">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a61d1-123">`https://example.net`&ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="a61d1-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a61d1-124">`https://www.example.com/foo.html`&ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="a61d1-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a61d1-125">`http://example.com/foo.html`&ndash; 다른 체계</span><span class="sxs-lookup"><span data-stu-id="a61d1-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a61d1-126">`https://example.com:9000/foo.html`&ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="a61d1-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="a61d1-127">CORS를 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-127">Enable CORS</span></span>

<span data-ttu-id="a61d1-128">CORS를 사용 하도록 설정 하는 방법에는 세 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="a61d1-129">미들웨어에서 [명명 된 정책](#np) 또는 [기본 정책을](#dp)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="a61d1-130">[끝점 라우팅을](#ecors)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a61d1-131">[[EnableCors]](#attr) 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="a61d1-132">명명 된 정책에서 [[EnableCors]](#attr) 특성을 사용 하면 CORS를 지 원하는 끝점을 제한 하는 가장 세밀 한 제어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="a61d1-133">각 접근 방식은 다음 섹션에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a61d1-134">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="a61d1-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="a61d1-135">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a61d1-136">다음 코드는 지정 된 원본을 사용 하 여 모든 앱의 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="a61d1-137">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="a61d1-137">The preceding code:</span></span>

* <span data-ttu-id="a61d1-138">정책 이름을로 `_myAllowSpecificOrigins`설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="a61d1-139">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a61d1-140">확장 메서드 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 를 호출 하 고 `_myAllowSpecificOrigins` CORS 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="a61d1-141">`UseCors`CORS 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="a61d1-142">에 대 `UseCors` 한 `UseRouting`호출은 앞 `UseAuthorization`에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="a61d1-143">자세한 내용은 [미들웨어 순서](xref:fundamentals/middleware/index#middleware-order)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="a61d1-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a61d1-145">람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a61d1-146">등의 [구성 옵션](#cors-policy-options) `WithOrigins`에 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="a61d1-147">모든 컨트롤러 `_myAllowSpecificOrigins` 끝점에 대해 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="a61d1-148">특정 끝점에 CORS 정책을 적용 하려면 [끝점 라우팅](#ecors) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="a61d1-149">끝점 라우팅을 사용 하 여 및에 대 `UseRouting` 한 `UseEndpoints`호출 사이에 CORS 미들웨어를 실행 하도록 구성 ***해야 합니다*** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="a61d1-150">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="a61d1-151"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a61d1-152">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="a61d1-153">다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="a61d1-154">참고: 지정 된 URL은 **not** 후행 슬래시 (`/`)를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a61d1-155">URL이로 `/`종료 되 면 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="a61d1-156">기본 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="a61d1-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="a61d1-157">다음 강조 표시 된 코드는 기본 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="a61d1-158">위의 코드는 모든 컨트롤러 끝점에 기본 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="a61d1-159">엔드포인트 라우팅을 사용하여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="a61d1-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="a61d1-160">현재를 사용 하 여 `RequireCors` 끝점 별로 CORS를 사용 하도록 설정 하면 [자동 실행 전 요청](#apf)을 지원 ***하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a61d1-161">자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/aspnetcore/issues/20709) 및 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [httpoptions]](#tcer)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="a61d1-162">끝점 라우팅을 사용 하면 확장 메서드 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="a61d1-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="a61d1-163">In the preceding code:</span></span>

* <span data-ttu-id="a61d1-164">`app.UseCors`CORS 미들웨어를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="a61d1-165">기본 정책은 구성 되지 않았기 때문에 CORS `app.UseCors()` 를 사용 하도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="a61d1-166">및 `/echo` 컨트롤러 끝점은 지정 된 정책을 사용 하 여 원본 간 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="a61d1-167">기본 `/echo2` 정책을 Razor 지정 하지 않았으므로 및 페이지 끝점에서 원본 간 요청을 허용 ***하지 않습니다*** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="a61d1-168">[[DisableCors]](#dc) 특성은를 ***사용*** `RequireCors`하 여 끝점 라우팅을 통해 설정 된 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="a61d1-169">위와 비슷한 코드 테스트에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a61d1-170">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="a61d1-170">Enable CORS with attributes</span></span>

<span data-ttu-id="a61d1-171">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성으로 cors를 사용 하도록 설정 하 고 cors가 필요한 끝점에만 명명 된 정책을 적용 하면 가장 많이 사용 되는 컨트롤이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="a61d1-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a61d1-173">특성 `[EnableCors]` 은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="a61d1-174">`[EnableCors]`기본 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="a61d1-175">`[EnableCors("{Policy String}")]`명명 된 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="a61d1-176">특성 `[EnableCors]` 은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="a61d1-177">페이지`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a61d1-177"> Page `PageModel`</span></span>
* <span data-ttu-id="a61d1-178">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="a61d1-178">Controller</span></span>
* <span data-ttu-id="a61d1-179">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="a61d1-179">Controller action method</span></span>

<span data-ttu-id="a61d1-180">특성을 사용 하는 `[EnableCors]` 컨트롤러, 페이지 모델 또는 작업 메서드에 다른 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="a61d1-181">특성이 컨트롤러, 페이지 모델 또는 동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 두 정책이 모두 적용 됩니다. ***both*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="a61d1-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a61d1-182">\*\*\*정책을 결합 하지 않는 것이 좋습니다. \*\*\*동일한 앱 ***에서 특성 또는 미들웨어를 사용 합니다.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="a61d1-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="a61d1-183">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a61d1-184">다음 코드는 두 개의 CORS 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="a61d1-185">CORS 요청 제한의 가장 적합 한 제어:</span><span class="sxs-lookup"><span data-stu-id="a61d1-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="a61d1-186">명명 `[EnableCors("MyPolicy")]` 된 정책에를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="a61d1-187">기본 정책을 정의 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-187">Don't define a default policy.</span></span>
* <span data-ttu-id="a61d1-188">[끝점 라우팅을](#ecors)사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a61d1-189">다음 섹션의 코드는 위의 목록을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="a61d1-190">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="a61d1-191">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="a61d1-191">Disable CORS</span></span>

<span data-ttu-id="a61d1-192">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 [끝점 라우팅으로](#ecors)설정 된 CORS를 사용 하지 않도록 설정 ***하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a61d1-193">다음 코드에서는 CORS 정책을 `"MyPolicy"`정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="a61d1-194">다음 코드는 `GetValues2` 작업에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="a61d1-195">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="a61d1-195">The preceding code:</span></span>

* <span data-ttu-id="a61d1-196">[끝점 라우팅을](#ecors)사용 하 여 CORS를 사용 하도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a61d1-197">[기본 CORS 정책을](#dp)정의 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="a61d1-198">[[EnableCors ("MyPolicy")]](#attr) 를 사용 하 여 `"MyPolicy"` 컨트롤러에 대 한 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="a61d1-199">`GetValues2` 메서드에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="a61d1-200">위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a61d1-201">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="a61d1-201">CORS policy options</span></span>

<span data-ttu-id="a61d1-202">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a61d1-203">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a61d1-204">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a61d1-205">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a61d1-206">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a61d1-207">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="a61d1-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a61d1-208">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a61d1-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>는에서 `Startup.ConfigureServices`호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a61d1-210">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a61d1-211">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-211">Set the allowed origins</span></span>

<span data-ttu-id="a61d1-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 원본에서 임의의 스키마 (`http` 또는 `https`)로 CORS 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a61d1-213">`AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a61d1-214">및 `AllowAnyOrigin` `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a61d1-215">앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="a61d1-216">`AllowAnyOrigin`실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a61d1-217">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a61d1-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 원본이 허용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 되는지 평가할 때 구성 된 와일드 카드 도메인에 대해 원본이 일치 하도록 설정 하는 함수로 정책의 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a61d1-219">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a61d1-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a61d1-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a61d1-221">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="a61d1-222">실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a61d1-223">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a61d1-224">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-224">Set the allowed request headers</span></span>

<span data-ttu-id="a61d1-225">[작성자 요청 헤더](https://xhr.spec.whatwg.org/#request)라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 하 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a61d1-226">모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a61d1-227">`AllowAnyHeader`실행 전 요청 및 [액세스 제어 요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="a61d1-228">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a61d1-229">로 `WithHeaders` 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은에 지정 된 헤더와 정확 `Access-Control-Request-Headers` 하 게 일치 하는 경우 `WithHeaders`에만 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="a61d1-230">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="a61d1-231">CORS 미들웨어는 다음 요청 헤더를 사용 하 여 `Content-Language` `WithHeaders`실행 전 요청을 거부 합니다 ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)).</span><span class="sxs-lookup"><span data-stu-id="a61d1-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="a61d1-232">앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a61d1-233">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a61d1-234">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-234">Set the exposed response headers</span></span>

<span data-ttu-id="a61d1-235">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a61d1-236">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a61d1-237">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a61d1-238">CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a61d1-239">앱에서 다른 헤더를 사용할 수 있도록 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a61d1-240">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="a61d1-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a61d1-241">자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a61d1-242">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a61d1-243">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a61d1-244">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를 `XMLHttpRequest.withCredentials` 로 `true`설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a61d1-245">직접 `XMLHttpRequest` 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a61d1-246">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a61d1-247">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a61d1-248">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-248">The server must allow the credentials.</span></span> <span data-ttu-id="a61d1-249">원본 간 자격 증명을 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="a61d1-250">HTTP 응답에는 서버 `Access-Control-Allow-Credentials` 에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a61d1-251">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되지 않은 경우 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a61d1-252">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a61d1-253">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a61d1-254">CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본으로 설정 ( `"*"` 모든 원본)을 사용할 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="a61d1-255">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="a61d1-255">Preflight requests</span></span>

<span data-ttu-id="a61d1-256">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 [옵션](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="a61d1-257">이 요청을 실행 [전 요청](https://developer.mozilla.org/docs/Glossary/Preflight_request)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="a61d1-258">다음 조건이 ***모두*** 충족 되는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="a61d1-259">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a61d1-260">앱은, `Accept`, `Accept-Language` `Content-Language`, `Content-Type`또는 `Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a61d1-261">헤더 `Content-Type` (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a61d1-262">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 `setRequestHeader` `XMLHttpRequest` 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a61d1-263">CORS 사양은 이러한 헤더 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="a61d1-264">`User-Agent`이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, `Host`또는 `Content-Length`)에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a61d1-265">다음은이 문서의 [CORS 테스트](#testc) 섹션에서 **[Put test]** 단추를 통해 수행 된 실행 전 요청과 유사한 응답의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="a61d1-266">실행 전 요청은 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="a61d1-267">여기에는 다음 헤더가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-267">It may include the following headers:</span></span>

* <span data-ttu-id="a61d1-268">[액세스 제어-요청 메서드](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a61d1-269">[액세스 제어-요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a61d1-270">앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:) `User-Agent`는 여기에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="a61d1-271">액세스 제어-허용 방법</span><span class="sxs-lookup"><span data-stu-id="a61d1-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="a61d1-272">실행 전 요청이 거부 되 면 앱은 응답을 `200 OK` 반환 하지만 CORS 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="a61d1-273">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="a61d1-274">거부 된 실행 전 요청에 대 한 예는이 문서의 [CORS 테스트](#testc) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="a61d1-275">콘솔 앱은 F12 도구를 사용 하 여 브라우저에 따라 다음과 같은 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="a61d1-276">Firefox: 크로스-원본 요청 차단 됨: 동일한 원본 정책이에서 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`원격 리소스를 읽을 것을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="a61d1-277">(원인: CORS 요청이 실패 했습니다.)</span><span class="sxs-lookup"><span data-stu-id="a61d1-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="a61d1-278">자세히 알아보기</span><span class="sxs-lookup"><span data-stu-id="a61d1-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="a61d1-279">Chromium based: ' ' 원본https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5https://cors3.azurewebsites.net에서 ' '의 Fetch에 대 한 액세스가 CORS 정책에 의해 차단 되었습니다. 실행 전 요청에 대 한 응답은 액세스 제어 확인을 통과 하지 않습니다. 요청한 리소스에 ' 액세스 제어-허용-원본 ' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a61d1-280">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="a61d1-281">특정 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a61d1-282">모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a61d1-283">브라우저의 설정 `Access-Control-Request-Headers`방법은 일관 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a61d1-284">다음 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-284">If either:</span></span>

* <span data-ttu-id="a61d1-285">헤더는 다음 이외의 값으로 설정 됩니다.`"*"`</span><span class="sxs-lookup"><span data-stu-id="a61d1-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="a61d1-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>가 호출 됩니다 .에는 `Accept`, `Content-Type`및를 `Origin`포함 하 고 지원 하려는 사용자 지정 헤더를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="a61d1-287">자동 실행 전 요청 코드</span><span class="sxs-lookup"><span data-stu-id="a61d1-287">Automatic preflight request code</span></span>

<span data-ttu-id="a61d1-288">CORS 정책이 적용 되는 경우 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="a61d1-289">에서를 `Startup.Configure`호출 `app.UseCors` 하 여 전역적으로</span><span class="sxs-lookup"><span data-stu-id="a61d1-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="a61d1-290">특성을 `[EnableCors]` 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="a61d1-291">ASP.NET Core는 실행 전 옵션 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="a61d1-292">현재를 사용 하 여 `RequireCors` 끝점 별로 CORS를 사용 하도록 설정 하면 자동 실행 전 요청을 지원 ***하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="a61d1-293">이 문서의 [CORS 테스트](#testc) 섹션에서는이 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="a61d1-294">[HttpOptions] 실행 전 요청에 대 한 특성</span><span class="sxs-lookup"><span data-stu-id="a61d1-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="a61d1-295">적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="a61d1-296">일부 시나리오에서는 그렇지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="a61d1-297">예를 들어 [끝점 라우팅과 함께 CORS](#ecors)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="a61d1-298">다음 코드는 [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 특성을 사용 하 여 OPTIONS 요청에 대 한 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="a61d1-299">위의 코드를 테스트 하는 방법에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a61d1-300">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-300">Set the preflight expiration time</span></span>

<span data-ttu-id="a61d1-301">헤더 `Access-Control-Max-Age` 는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a61d1-302">이 헤더를 설정 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a61d1-303">CORS 작동 방법</span><span class="sxs-lookup"><span data-stu-id="a61d1-303">How CORS works</span></span>

<span data-ttu-id="a61d1-304">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a61d1-305">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="a61d1-306">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a61d1-307">예를 들어 악의적인 행위자는 사이트에 대해 [XSS (교차 사이트 스크립팅)](xref:security/cross-site-scripting) 를 사용 하 고 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행 하 여 정보를 도용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a61d1-308">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="a61d1-309">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a61d1-310">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a61d1-311">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a61d1-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a61d1-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a61d1-313">Postman</span><span class="sxs-lookup"><span data-stu-id="a61d1-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a61d1-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="a61d1-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a61d1-315">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a61d1-316">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a61d1-317">CORS가 없는 브라우저는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="a61d1-318">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a61d1-319">JSONP는 XHR을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a61d1-320">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a61d1-321">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a61d1-322">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a61d1-323">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a61d1-324">배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에 대 한 [테스트 배치 단추](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="a61d1-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="a61d1-325">다음은 [값](https://cors3.azurewebsites.net/) 테스트 단추에서로 `https://cors1.azurewebsites.net/api/values`의 원본 간 요청 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="a61d1-326">`Origin` 헤더:</span><span class="sxs-lookup"><span data-stu-id="a61d1-326">The `Origin` header:</span></span>

* <span data-ttu-id="a61d1-327">요청을 수행 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="a61d1-328">는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="a61d1-329">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="a61d1-330">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="a61d1-331">**요청 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-331">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="a61d1-332">요청 `OPTIONS` 에서 서버는 응답에 **응답 헤더** `Access-Control-Allow-Origin: {allowed origin}` 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="a61d1-333">예를 들어, 배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)인 [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 단추 `OPTIONS` 요청은 다음 헤더를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="a61d1-334">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="a61d1-335">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="a61d1-336">**요청 헤더**</span><span class="sxs-lookup"><span data-stu-id="a61d1-336">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="a61d1-337">위의 **응답 헤더**에서 서버는 응답에 [액세스 제어 허용 원본](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="a61d1-338">이 `https://cors1.azurewebsites.net` 헤더의 값은 요청의 `Origin` 헤더와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="a61d1-339">가 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 호출 `Access-Control-Allow-Origin: *`되 면 와일드 카드 값이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="a61d1-340">`AllowAnyOrigin`모든 원본을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="a61d1-341">응답에 `Access-Control-Allow-Origin` 헤더가 포함 되지 않은 경우 원본 간 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a61d1-342">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a61d1-343">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="a61d1-344">표시 옵션 요청</span><span class="sxs-lookup"><span data-stu-id="a61d1-344">Display OPTIONS requests</span></span>

<span data-ttu-id="a61d1-345">기본적으로 Chrome 및 Edge 브라우저는 F12 도구의 네트워크 탭에서 옵션 요청을 표시 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="a61d1-346">이러한 브라우저에 옵션 요청을 표시 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="a61d1-347">`chrome://flags/#out-of-blink-cors` 또는 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="a61d1-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="a61d1-348">플래그를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-348">disable the flag.</span></span>
* <span data-ttu-id="a61d1-349">시작할지.</span><span class="sxs-lookup"><span data-stu-id="a61d1-349">restart.</span></span>

<span data-ttu-id="a61d1-350">Firefox에는 기본적으로 옵션 요청이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a61d1-351">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="a61d1-351">CORS in IIS</span></span>

<span data-ttu-id="a61d1-352">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a61d1-353">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="a61d1-354">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="a61d1-354">Test CORS</span></span>

<span data-ttu-id="a61d1-355">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에는 CORS를 테스트 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="a61d1-356">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="a61d1-357">이 샘플은 페이지가 추가 된 Razor API 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="a61d1-358">`WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="a61d1-359">다음 `ValuesController` 은 테스트에 대 한 끝점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="a61d1-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 는 [Rick RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 패키지를 통해 제공 되며 경로 정보를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="a61d1-361">다음 방법 중 하나를 사용 하 여 앞의 샘플 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="a61d1-362">에서 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)배포 된 샘플 앱을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="a61d1-363">샘플을 다운로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="a61d1-364">의 `https://localhost:5001`기본 URL을 `dotnet run` 사용 하 여 샘플을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="a61d1-365">URL에 대해 44398로 설정 된 포트를 사용 하 여 Visual Studio에서 샘플 `https://localhost:44398`을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="a61d1-366">F12 도구를 사용 하 여 브라우저 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="a61d1-367">**값** 단추를 선택 하 고 **네트워크** 탭에서 헤더를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="a61d1-368">**테스트 배치** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-368">Select the **PUT test** button.</span></span> <span data-ttu-id="a61d1-369">옵션 요청 표시에 대 한 지침은 [옵션 요청 표시](#options) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="a61d1-370">**Put 테스트** 는 옵션 실행 전 요청 및 PUT 요청 이라는 두 개의 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="a61d1-371">실패 한 **`GetValues2 [DisableCors]`** CORS 요청을 트리거하는 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="a61d1-372">문서에 설명 된 것 처럼 응답은 200 성공을 반환 하지만 CORS 요청은 수행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="a61d1-373">CORS 오류를 보려면 **콘솔** 탭을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="a61d1-374">브라우저에 따라 다음과 같은 오류가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="a61d1-375">원본 `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` 에서 가져오기에 대 한 액세스가 CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a61d1-376">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="a61d1-377">CORS 사용 끝점은 [말아](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a61d1-378">도구를 사용 하는 경우 `Origin` 헤더에 의해 지정 된 요청의 원점은 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a61d1-379">요청이 `Origin` 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우:</span><span class="sxs-lookup"><span data-stu-id="a61d1-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a61d1-380">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a61d1-381">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="a61d1-382">다음 명령은를 사용 `curl` 하 여 정보를 포함 하는 옵션 요청을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="a61d1-383">끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="a61d1-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="a61d1-384">현재를 사용 하 여 `RequireCors` 끝점 별로 CORS를 사용 하도록 설정 하면 [자동 실행 전 요청](#apf)을 지원 ***하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a61d1-385">끝점 라우팅을 사용 하 여 [CORS를 사용 하도록 설정 하](#ecors)는 다음 코드를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="a61d1-386">테스트를 `TodoItems1Controller` 위한 끝점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="a61d1-387">배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=1) 에서 이전 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="a61d1-388">끝점이 실행 전 요청에 응답 하 `[EnableCors]` 고이 요청에 응답 하기 때문에 **Delete [EnableCors]** 및 **GET [EnableCors]** 단추가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="a61d1-389">다른 끝점은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-389">The other endpoints fails.</span></span> <span data-ttu-id="a61d1-390">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 가 다음을 보내기 때문에 **GET** 단추가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="a61d1-391">다음 `TodoItems2Controller` 은 비슷한 끝점을 제공 하지만 OPTIONS 요청에 응답 하는 명시적인 코드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="a61d1-392">배포 된 샘플의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=2) 에서 이전 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="a61d1-393">**컨트롤러** 드롭다운 목록에서 실행 **전** 을 선택 하 고 **컨트롤러를 설정**합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="a61d1-394">`TodoItems2Controller` 끝점에 대 한 모든 CORS 호출이 성공 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a61d1-395">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a61d1-395">Additional resources</span></span>

* [<span data-ttu-id="a61d1-396">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="a61d1-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a61d1-397">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="a61d1-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a61d1-398">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a61d1-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a61d1-399">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a61d1-400">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a61d1-401">이러한 제한 사항을 *동일 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a61d1-402">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a61d1-403">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="a61d1-404">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a61d1-405">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="a61d1-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a61d1-406">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a61d1-407">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a61d1-408">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a61d1-409">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a61d1-410">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a61d1-411">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a61d1-412">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a61d1-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a61d1-413">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="a61d1-413">Same origin</span></span>

<span data-ttu-id="a61d1-414">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a61d1-415">이러한 두 Url의 출처는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a61d1-416">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a61d1-417">`https://example.net`&ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="a61d1-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a61d1-418">`https://www.example.com/foo.html`&ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="a61d1-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a61d1-419">`http://example.com/foo.html`&ndash; 다른 체계</span><span class="sxs-lookup"><span data-stu-id="a61d1-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a61d1-420">`https://example.com:9000/foo.html`&ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="a61d1-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="a61d1-421">Internet Explorer는 원본을 비교할 때 포트를 고려 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a61d1-422">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="a61d1-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="a61d1-423">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a61d1-424">다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="a61d1-425">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="a61d1-425">The preceding code:</span></span>

* <span data-ttu-id="a61d1-426">정책 이름을 "\_myAllowSpecificOrigins"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="a61d1-427">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a61d1-428">CORS를 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 사용 하도록 설정 하는 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="a61d1-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a61d1-430">람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a61d1-431">등의 [구성 옵션](#cors-policy-options) `WithOrigins`에 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="a61d1-432"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a61d1-433">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="a61d1-434">메서드 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="a61d1-435">참고: URL은 후행 **not** 슬래시 (`/`)를 포함 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a61d1-436">URL이로 `/`종료 되 면 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="a61d1-437">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="a61d1-438">참고: `UseCors` 이전 `UseMvc`에를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="a61d1-439">페이지 [, 컨트롤러 및 Razor 작업 메서드에서 cors를 사용](#ecors) 하 여 페이지/컨트롤러/동작 수준에서 cors 정책 적용을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="a61d1-440">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a61d1-441">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="a61d1-441">Enable CORS with attributes</span></span>

<span data-ttu-id="a61d1-442">[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a61d1-443">특성 `[EnableCors]` 은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="a61d1-444">기본 `[EnableCors]` 정책을 지정 하 고 `[EnableCors("{Policy String}")]` 정책을 지정 하려면를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="a61d1-445">특성 `[EnableCors]` 은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="a61d1-446">페이지`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a61d1-446"> Page `PageModel`</span></span>
* <span data-ttu-id="a61d1-447">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="a61d1-447">Controller</span></span>
* <span data-ttu-id="a61d1-448">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="a61d1-448">Controller action method</span></span>

<span data-ttu-id="a61d1-449">`[EnableCors]` 특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="a61d1-450">`[EnableCors]` 특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 두 정책이 ***모두*** 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a61d1-451">정책을 결합 ***하지 않는*** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="a61d1-452">`[EnableCors]` 특성 또는 미들웨어를 사용 합니다.**not both**</span><span class="sxs-lookup"><span data-stu-id="a61d1-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="a61d1-453">를 사용 `[EnableCors]`하는 경우 기본 정책을 정의 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="a61d1-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="a61d1-454">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a61d1-455">다음 코드에서는 CORS 기본 정책과 라는 `"AnotherPolicy"`정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="a61d1-456">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="a61d1-456">Disable CORS</span></span>

<span data-ttu-id="a61d1-457">[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a61d1-458">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="a61d1-458">CORS policy options</span></span>

<span data-ttu-id="a61d1-459">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a61d1-460">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a61d1-461">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a61d1-462">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a61d1-463">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a61d1-464">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="a61d1-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a61d1-465">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a61d1-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>는에서 `Startup.ConfigureServices`호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a61d1-467">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a61d1-468">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-468">Set the allowed origins</span></span>

<span data-ttu-id="a61d1-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 원본에서 임의의 스키마 (`http` 또는 `https`)로 CORS 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a61d1-470">`AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a61d1-471">및 `AllowAnyOrigin` `AllowCredentials` 지정은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a61d1-472">보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="a61d1-473">`AllowAnyOrigin`실행 전 요청 및 `Access-Control-Allow-Origin` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a61d1-474">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a61d1-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 원본이 허용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 되는지 평가할 때 구성 된 와일드 카드 도메인에 대해 원본이 일치 하도록 설정 하는 함수로 정책의 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a61d1-476">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a61d1-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a61d1-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a61d1-478">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="a61d1-479">실행 전 요청 및 `Access-Control-Allow-Methods` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a61d1-480">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a61d1-481">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-481">Set the allowed request headers</span></span>

<span data-ttu-id="a61d1-482">*작성자 요청 헤더*라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 하 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a61d1-483">모든 작성자 요청 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a61d1-484">이 설정은 실행 전 요청 및 `Access-Control-Request-Headers` 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="a61d1-485">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a61d1-486">CORS 미들웨어는 `Access-Control-Request-Headers` 항상에 있는 4 개의 헤더를 CorsPolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="a61d1-487">이 헤더 목록에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="a61d1-488">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="a61d1-489">가 항상 허용 목록 이므로 `Content-Language` CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a61d1-490">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-490">Set the exposed response headers</span></span>

<span data-ttu-id="a61d1-491">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a61d1-492">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a61d1-493">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a61d1-494">CORS 사양은 이러한 헤더 *단순 응답 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a61d1-495">앱에서 다른 헤더를 사용할 수 있도록 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a61d1-496">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="a61d1-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a61d1-497">자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a61d1-498">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a61d1-499">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a61d1-500">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를 `XMLHttpRequest.withCredentials` 로 `true`설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a61d1-501">직접 `XMLHttpRequest` 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a61d1-502">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a61d1-503">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a61d1-504">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-504">The server must allow the credentials.</span></span> <span data-ttu-id="a61d1-505">원본 간 자격 증명을 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="a61d1-506">HTTP 응답에는 서버 `Access-Control-Allow-Credentials` 에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a61d1-507">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함 되지 않은 경우 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a61d1-508">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a61d1-509">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a61d1-510">CORS 사양은 `Access-Control-Allow-Credentials` 헤더가 있는 경우 원본으로 설정 ( `"*"` 모든 원본)을 사용할 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="a61d1-511">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="a61d1-511">Preflight requests</span></span>

<span data-ttu-id="a61d1-512">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="a61d1-513">이 요청을 실행 *전 요청*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="a61d1-514">다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="a61d1-515">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a61d1-516">앱은, `Accept`, `Accept-Language` `Content-Language`, `Content-Type`또는 `Last-Event-ID`이외의 요청 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a61d1-517">헤더 `Content-Type` (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a61d1-518">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 `setRequestHeader` `XMLHttpRequest` 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a61d1-519">CORS 사양은 이러한 헤더 *작성자 요청 헤더*를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="a61d1-520">`User-Agent`이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, `Host`또는 `Content-Length`)에는 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a61d1-521">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-521">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="a61d1-522">사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="a61d1-523">여기에는 두 가지 특수 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-523">It includes two special headers:</span></span>

* <span data-ttu-id="a61d1-524">`Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a61d1-525">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a61d1-526">앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:) `User-Agent`는 여기에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="a61d1-527">적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core는 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="a61d1-528">실행 [전 요청에 대 한 [Httpoptions] 특성을](#pro)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a61d1-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="a61d1-529">CORS 실행 전 요청에는 `Access-Control-Request-Headers` 헤더가 포함 될 수 있으며,이는 실제 요청과 함께 전송 되는 헤더를 서버에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="a61d1-530">특정 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a61d1-531">모든 작성자 요청 헤더를 허용 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a61d1-532">브라우저의 설정 `Access-Control-Request-Headers`방법은 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a61d1-533">헤더를 `"*"` (또는을 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>하는 경우) 이외의 값으로 설정 하는 경우, `Accept`및 `Content-Type`를 포함 `Origin`하 고 지원 하려는 사용자 지정 헤더를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="a61d1-534">다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="a61d1-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="a61d1-535">응답에는 허용 `Access-Control-Allow-Methods` 된 메서드를 나열 하는 헤더와 허용 `Access-Control-Allow-Headers` 되는 헤더를 나열 하는 헤더 (선택 사항)가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="a61d1-536">실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="a61d1-537">실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a61d1-538">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a61d1-539">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="a61d1-539">Set the preflight expiration time</span></span>

<span data-ttu-id="a61d1-540">헤더 `Access-Control-Max-Age` 는 실행 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a61d1-541">이 헤더를 설정 하려면 다음 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a61d1-542">CORS 작동 방법</span><span class="sxs-lookup"><span data-stu-id="a61d1-542">How CORS works</span></span>

<span data-ttu-id="a61d1-543">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a61d1-544">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="a61d1-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="a61d1-545">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a61d1-546">예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a61d1-547">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="a61d1-548">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a61d1-549">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a61d1-550">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a61d1-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a61d1-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a61d1-552">Postman</span><span class="sxs-lookup"><span data-stu-id="a61d1-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a61d1-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="a61d1-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a61d1-554">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a61d1-555">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a61d1-556">브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="a61d1-557">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a61d1-558">JSONP는 XHR을 사용 하지 않으며 `<script>` 태그를 사용 하 여 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a61d1-559">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a61d1-560">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a61d1-561">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a61d1-562">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a61d1-563">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="a61d1-564">헤더 `Origin` 는 요청을 만드는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="a61d1-565">헤더 `Origin` 는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-565">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="a61d1-566">서버에서 요청을 허용 하는 경우 응답에 `Access-Control-Allow-Origin` 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="a61d1-567">이 헤더의 값은 요청의 `Origin` 헤더와 일치 하거나 와일드 카드 값 `"*"`입니다. 즉, 모든 원본이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="a61d1-568">응답에 `Access-Control-Allow-Origin` 헤더가 포함 되지 않은 경우 원본 간 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a61d1-569">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a61d1-570">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="a61d1-571">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="a61d1-571">Test CORS</span></span>

<span data-ttu-id="a61d1-572">CORS를 테스트 하려면:</span><span class="sxs-lookup"><span data-stu-id="a61d1-572">To test CORS:</span></span>

1. <span data-ttu-id="a61d1-573">[API 프로젝트를 만듭니다](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="a61d1-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="a61d1-574">또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="a61d1-575">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="a61d1-576">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a61d1-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="a61d1-577">`WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="a61d1-578">웹 앱 프로젝트 (Razor 페이지 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="a61d1-579">이 샘플에서는 Razor 페이지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="a61d1-580">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="a61d1-581">다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="a61d1-582">위의 코드에서를 배포 된 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 앱의 URL로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="a61d1-583">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-583">Deploy the API project.</span></span> <span data-ttu-id="a61d1-584">예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="a61d1-585">바탕 화면 Razor 에서 페이지 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="a61d1-586">F12 도구를 사용 하 여 오류 메시지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="a61d1-587">에서 `WithOrigins` localhost 원본을 제거 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="a61d1-588">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="a61d1-589">예를 들어 Visual Studio에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="a61d1-590">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-590">Test with the client app.</span></span> <span data-ttu-id="a61d1-591">CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="a61d1-592">F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="a61d1-593">브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).</span><span class="sxs-lookup"><span data-stu-id="a61d1-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="a61d1-594">Microsoft Edge 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="a61d1-595">**SEC7120: [CORS] 원본 `https://localhost:44375` 에서 원본 간 리소스 `https://localhost:44375` 에 대 한 액세스 제어-원본 응답 헤더를 찾을 수 없습니다.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="a61d1-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="a61d1-596">Chrome 사용:</span><span class="sxs-lookup"><span data-stu-id="a61d1-596">Using Chrome:</span></span>

     <span data-ttu-id="a61d1-597">**원본 `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` 에서의 XMLHttpRequest에 대 한 액세스가 CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어--원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="a61d1-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="a61d1-598">CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a61d1-599">도구를 사용 하는 경우 `Origin` 헤더에 의해 지정 된 요청의 원점은 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a61d1-600">요청이 `Origin` 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우:</span><span class="sxs-lookup"><span data-stu-id="a61d1-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a61d1-601">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a61d1-602">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a61d1-603">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="a61d1-603">CORS in IIS</span></span>

<span data-ttu-id="a61d1-604">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a61d1-605">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a61d1-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a61d1-606">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a61d1-606">Additional resources</span></span>

* [<span data-ttu-id="a61d1-607">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="a61d1-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a61d1-608">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="a61d1-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
