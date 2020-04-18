---
title: 코어에서 CORS 간 요청(CORS) ASP.NET
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용하거나 거부하는 표준으로 CORS를 어떻게 사용할 수 있는지 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642694"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="c9f66-103">코어에서 CORS 간 요청(CORS) ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c9f66-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9f66-104">[릭 앤더슨과](https://twitter.com/RickAndMSFT) [커크 라킨](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c9f66-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c9f66-105">이 문서에서는 ASP.NET 코어 앱에서 CORS를 사용하도록 설정하는 방법을 보여 주며, CORS를 활성화하는 방법을 보여 주시고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c9f66-106">브라우저 보안은 웹 페이지가 웹 페이지를 제공한 도메인과 다른 도메인에 대한 요청을 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c9f66-107">이 제한을 *동일한 원본 정책이라고*합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c9f66-108">동일한 원본 정책은 악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c9f66-109">경우에 따라 다른 사이트에서 앱에 대한 원본 간 요청을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="c9f66-110">자세한 내용은 [모질라 CORS 문서를](https://developer.mozilla.org/docs/Web/HTTP/CORS)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c9f66-111">[원본 간 리소스](https://www.w3.org/TR/cors/) 공유(CORS):</span><span class="sxs-lookup"><span data-stu-id="c9f66-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c9f66-112">서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c9f66-113">보안 기능이 **아니며** CORS는 보안을 완화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c9f66-114">CORS를 허용하면 API가 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c9f66-115">자세한 내용은 [CORS 작동 방식을](#how-cors)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c9f66-116">서버가 다른 요청을 거부하면서 일부 원본 간 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c9f66-117">[JSONP와](/dotnet/framework/wcf/samples/jsonp)같은 이전 기술보다 더 안전하고 유연합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c9f66-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9f66-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c9f66-119">동일한 원산지</span><span class="sxs-lookup"><span data-stu-id="c9f66-119">Same origin</span></span>

<span data-ttu-id="c9f66-120">두 URL은 동일한 구성표, 호스트 및[포트(RFC 6454)가](https://tools.ietf.org/html/rfc6454)있는 경우 동일한 출처를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c9f66-121">이 두 URL의 출처는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c9f66-122">이러한 URL의 출처는 이전 두 URL과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c9f66-123">`https://example.net`&ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="c9f66-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c9f66-124">`https://www.example.com/foo.html`&ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="c9f66-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c9f66-125">`http://example.com/foo.html`&ndash; 다른 구성표</span><span class="sxs-lookup"><span data-stu-id="c9f66-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c9f66-126">`https://example.com:9000/foo.html`&ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="c9f66-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="c9f66-127">CORS를 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-127">Enable CORS</span></span>

<span data-ttu-id="c9f66-128">CORS를 활성화하는 방법에는 세 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="c9f66-129">[미들웨어에서 명명된 정책](#np) 또는 [기본 정책을](#dp)사용 하 여 .</span><span class="sxs-lookup"><span data-stu-id="c9f66-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="c9f66-130">[끝점 라우팅](#ecors)을 사용 하 여 .</span><span class="sxs-lookup"><span data-stu-id="c9f66-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c9f66-131">[[인에이블코르] 특성을](#attr) 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="c9f66-132">명명된 정책과 함께 [[EnableCors] 특성을](#attr) 사용하면 CORS를 지원하는 끝점을 제한하는 데 가장 좋은 제어가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="c9f66-133">각 접근 방식은 다음 섹션에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c9f66-134">명명된 정책 및 미들웨어가 있는 CORS</span><span class="sxs-lookup"><span data-stu-id="c9f66-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="c9f66-135">CORS 미들웨어는 교차 원산지 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c9f66-136">다음 코드는 지정된 출처를 가진 모든 앱의 끝점에 CORS 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="c9f66-137">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="c9f66-137">The preceding code:</span></span>

* <span data-ttu-id="c9f66-138">정책 이름을 로 `_myAllowSpecificOrigins`설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="c9f66-139">정책 이름은 임의입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c9f66-140">확장 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 메서드를 호출 하 `_myAllowSpecificOrigins` 고 CORS 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="c9f66-141">`UseCors`CORS 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="c9f66-142">호출은 `UseCors` `UseRouting`에 배치되어야 `UseAuthorization`합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="c9f66-143">자세한 내용은 [미들웨어 주문을](xref:fundamentals/middleware/index#middleware-order)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="c9f66-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식으로](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c9f66-145">람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 취합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c9f66-146">[과](#cors-policy-options)같은 `WithOrigins`구성 옵션은 이 문서의 후반부에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="c9f66-147">모든 `_myAllowSpecificOrigins` 컨트롤러 끝점에 대해 CORS 정책을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="c9f66-148">특정 끝점에 CORS 정책을 적용하려면 [끝점 라우팅을](#ecors) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="c9f66-149">끝점 라우팅의 경우 CORS 미들웨어는 에 대한 호출 `UseRouting` 과 `UseEndpoints`간에 실행하도록 ***구성되어야 합니다.***</span><span class="sxs-lookup"><span data-stu-id="c9f66-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="c9f66-150">위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#testc) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="c9f66-151">메서드 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 호출은 앱의 서비스 컨테이너에 CORS 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c9f66-152">자세한 내용은 이 문서의 [CORS 정책 옵션을](#cpo) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="c9f66-153">메서드는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 다음 코드와 같이 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="c9f66-154">참고: 지정된 URL에는 후행 슬래시()가`/` **포함되어서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c9f66-155">URL이 `/`으로 종료되면 비교가 `false` 반환되고 헤더가 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="c9f66-156">기본 정책 및 미들웨어가 있는 CORS</span><span class="sxs-lookup"><span data-stu-id="c9f66-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="c9f66-157">다음 강조 표시된 코드는 기본 CORS 정책을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="c9f66-158">앞의 코드는 모든 컨트롤러 끝점에 기본 CORS 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="c9f66-159">엔드포인트 라우팅을 사용하여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="c9f66-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="c9f66-160">현재 를 사용하여 `RequireCors` 엔드포인트별로 CORS를 사용하도록 설정하는 것은 자동 [프리플라이트 요청을](#apf)지원하지 ***않습니다.***</span><span class="sxs-lookup"><span data-stu-id="c9f66-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c9f66-161">자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/aspnetcore/issues/20709) 및 [끝점 라우팅이 있는 테스트 CORS 및 [HttpOptions]를](#tcer)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="c9f66-162">끝점 라우팅을 사용하면 다음과 같은 확장 메서드 집합을 사용하여 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> 끝점별로 CORS를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="c9f66-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="c9f66-163">In the preceding code:</span></span>

* <span data-ttu-id="c9f66-164">`app.UseCors`CORS 미들웨어를 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="c9f66-165">기본 정책이 구성되지 않았기 때문에 `app.UseCors()` 단독으로는 CORS를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="c9f66-166">`/echo` 및 컨트롤러 끝점은 지정된 정책을 사용하여 원본 간 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="c9f66-167">`/echo2` 및 Razor 페이지 끝점은 기본 정책이 지정되지 않았기 때문에 원본 간 요청을 허용하지 ***않습니다.***</span><span class="sxs-lookup"><span data-stu-id="c9f66-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="c9f66-168">[[DisableCors]](#dc) 특성은 `RequireCors`에서 끝점 라우팅에 의해 활성화된 CORS를 사용하지 ***않도록 설정하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="c9f66-169">선행과 유사한 코드 테스트에 대한 지침은 [끝점 라우팅이 포함된 테스트 CORS 및 [HttpOptions]를](#tcer) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c9f66-170">특성을 사용하여 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="c9f66-170">Enable CORS with attributes</span></span>

<span data-ttu-id="c9f66-171">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성으로 CORS를 사용하도록 설정하고 CORS가 필요한 끝점에만 명명된 정책을 적용하면 최고의 컨트롤이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="c9f66-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용하는 대안을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c9f66-173">이 `[EnableCors]` 특성을 사용하면 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="c9f66-174">`[EnableCors]`기본 정책을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="c9f66-175">`[EnableCors("{Policy String}")]`지정된 정책을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="c9f66-176">특성을 `[EnableCors]` 다음에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="c9f66-177">면도기 페이지`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c9f66-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="c9f66-178">Controller</span><span class="sxs-lookup"><span data-stu-id="c9f66-178">Controller</span></span>
* <span data-ttu-id="c9f66-179">컨트롤러 작업 메서드</span><span class="sxs-lookup"><span data-stu-id="c9f66-179">Controller action method</span></span>

<span data-ttu-id="c9f66-180">특성이 있는 컨트롤러, 페이지 모델 또는 작업 메서드에 다른 정책을 적용할 수 있습니다. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="c9f66-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="c9f66-181">특성이 `[EnableCors]` 컨트롤러, 페이지 모델 또는 작업 메서드에 적용되고 중간 웨어에서 CORS가 활성화되면 두 정책이 ***모두*** 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c9f66-182">\*\*\*정책을 결합하지 않도록 하는 것이 좋습니다. \*\*\* `[EnableCors]` ***동일한 앱에서 모두 속성이나 미들웨어를*** 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="c9f66-183">다음 코드는 각 메서드에 다른 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c9f66-184">다음 코드는 두 개의 CORS 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="c9f66-185">CORS 요청을 제한하는 가장 미세한 제어를 위해:</span><span class="sxs-lookup"><span data-stu-id="c9f66-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="c9f66-186">명명된 정책과 함께 사용합니다. `[EnableCors("MyPolicy")]`</span><span class="sxs-lookup"><span data-stu-id="c9f66-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="c9f66-187">기본 정책을 정의하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-187">Don't define a default policy.</span></span>
* <span data-ttu-id="c9f66-188">[끝점 라우팅을](#ecors)사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9f66-189">다음 섹션의 코드는 위의 목록을 충족합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="c9f66-190">위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#testc) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="c9f66-191">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="c9f66-191">Disable CORS</span></span>

<span data-ttu-id="c9f66-192">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 [끝점 라우팅에](#ecors)의해 활성화된 CORS를 사용하지 ***않도록 설정하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9f66-193">다음 코드는 CORS 정책을 `"MyPolicy"`정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="c9f66-194">다음 코드는 작업에 대한 `GetValues2` CORS를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="c9f66-195">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="c9f66-195">The preceding code:</span></span>

* <span data-ttu-id="c9f66-196">[끝점 라우팅을](#ecors)사용하여 CORS를 활성화하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="c9f66-197">[기본 CORS 정책을](#dp)정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="c9f66-198">[[인에이블코르("MyPolicy")]를](#attr) `"MyPolicy"` 사용하여 컨트롤러에 대한 CORS 정책을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="c9f66-199">메서드에 대 한 `GetValues2` CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="c9f66-200">앞의 코드 테스트에 대한 지침은 [CORS](#testc) 테스트를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c9f66-201">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="c9f66-201">CORS policy options</span></span>

<span data-ttu-id="c9f66-202">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양한 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c9f66-203">허용된 원원 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c9f66-204">허용되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c9f66-205">허용된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c9f66-206">노출된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c9f66-207">원본 간 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="c9f66-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c9f66-208">비행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c9f66-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>에서 `Startup.ConfigureServices`호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c9f66-210">일부 옵션의 경우 [먼저 CORS 작동 방식](#how-cors) 섹션을 읽는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c9f66-211">허용된 원원 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-211">Set the allowed origins</span></span>

<span data-ttu-id="c9f66-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 구성표(또는)를`http` `https`사용하여 모든 원본의 CORS 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c9f66-213">`AllowAnyOrigin`모든 웹 *사이트가* 앱에 대한 교차 원산지 요청을 할 수 있기 때문에 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c9f66-214">`AllowAnyOrigin` 지정하고 `AllowCredentials` 안전하지 않은 구성이며 사이트 간 요청이 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c9f66-215">CORS 서비스는 앱이 두 메서드로 구성될 때 잘못된 CORS 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="c9f66-216">`AllowAnyOrigin`비행 전 요청 `Access-Control-Allow-Origin` 및 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c9f66-217">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9f66-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 오리진이 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 허용되는지 평가할 때 원본이 구성된 와일드카드 도메인과 일치하도록 허용하는 함수로 정책의 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c9f66-219">허용되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c9f66-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c9f66-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c9f66-221">HTTP 메서드를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="c9f66-222">비행 전 요청 `Access-Control-Allow-Methods` 및 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c9f66-223">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c9f66-224">허용된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-224">Set the allowed request headers</span></span>

<span data-ttu-id="c9f66-225">[작성자 요청 헤더라고](https://xhr.spec.whatwg.org/#request)하는 CORS 요청에서 특정 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 보낼 수 있도록 하려면 허용된 헤더를 호출하고 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c9f66-226">모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 [호출합니다.](https://www.w3.org/TR/cors/#author-request-headers)</span><span class="sxs-lookup"><span data-stu-id="c9f66-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c9f66-227">`AllowAnyHeader`는 사전 플라이트 요청 및 [액세스 제어-요청-헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="c9f66-228">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9f66-229">CORS 미들웨어 정책이 지정된 특정 헤더와 일치하는 `WithHeaders` 경우 에 전송된 헤더가 에 `Access-Control-Request-Headers` `WithHeaders`명시된 헤더와 정확히 일치하는 경우에만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="c9f66-230">예를 들어 다음과 같이 구성된 앱을 고려해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="c9f66-231">CORS 미들웨어는 다음 요청 헤더를 사용 `Content-Language` 하 고 사전 플라이트 요청을 거부 `WithHeaders`하기 때문에[(headerNames.ContentLanguage)](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)다음에 나열 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="c9f66-232">앱이 *200 OK* 응답을 반환하지만 CORS 헤더를 다시 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c9f66-233">따라서 브라우저는 원본 간 요청을 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c9f66-234">노출된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-234">Set the exposed response headers</span></span>

<span data-ttu-id="c9f66-235">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c9f66-236">자세한 내용은 [W3C 교차 원본 리소스 공유(용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c9f66-237">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c9f66-238">CORS 사양은 이러한 *헤더간단한 응답 헤더를 호출합니다.*</span><span class="sxs-lookup"><span data-stu-id="c9f66-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c9f66-239">앱에서 다른 헤더를 사용할 수 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>있도록 하려면 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c9f66-240">원본 간 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="c9f66-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c9f66-241">자격 증명에는 CORS 요청에서 특별한 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c9f66-242">기본적으로 브라우저는 원본 간 요청이 있는 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c9f66-243">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c9f66-244">원본 간 요청이 있는 자격 증명을 보내려면 `true`클라이언트를 로 설정해야 `XMLHttpRequest.withCredentials` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c9f66-245">직접 `XMLHttpRequest` 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c9f66-246">jQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c9f66-247">가져오기 [API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용 :</span><span class="sxs-lookup"><span data-stu-id="c9f66-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c9f66-248">서버는 자격 증명을 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-248">The server must allow the credentials.</span></span> <span data-ttu-id="c9f66-249">원본 간 자격 증명을 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>허용하려면 다음을 호출하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="c9f66-250">HTTP 응답에는 `Access-Control-Allow-Credentials` 서버가 원본 간 요청에 대한 자격 증명을 허용한다는 것을 브라우저에 알리는 헤더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c9f66-251">브라우저가 자격 증명을 전송하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함되지 않으면 브라우저는 앱에 대한 응답을 노출하지 않으며 원본 간 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c9f66-252">원본 간 자격 증명을 허용하는 것은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c9f66-253">다른 도메인의 웹 사이트는 사용자가 모르는 새앱 한 사용자의 자격 증명을 사용자 없이 사용자를 대신하여 앱으로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c9f66-254">또한 CORS 사양에 `"*"` `Access-Control-Allow-Credentials` 따르면 헤더가 있는 경우 (모든 원본)에 대한 설정이 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="c9f66-255">비행 전 요청</span><span class="sxs-lookup"><span data-stu-id="c9f66-255">Preflight requests</span></span>

<span data-ttu-id="c9f66-256">일부 CORS 요청의 경우 브라우저는 실제 요청을 하기 전에 추가 [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="c9f66-257">이 요청을 [프리플라이트 요청이라고](https://developer.mozilla.org/docs/Glossary/Preflight_request)합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="c9f66-258">브라우저는 다음 ***조건이 모두*** true인 경우 프리플라이트 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="c9f66-259">요청 방법은 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c9f66-260">앱에서 요청 `Accept`헤더를 " `Accept-Language` `Content-Language` `Content-Type`또는 `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="c9f66-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c9f66-261">헤더(설정된 `Content-Type` 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c9f66-262">클라이언트 요청에 대해 설정된 요청 헤더에 대한 규칙은 앱이 `setRequestHeader` `XMLHttpRequest` 개체를 호출하여 설정하는 헤더에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c9f66-263">CORS 사양은 이러한 헤더 [작성자 요청 헤더를 호출합니다.](https://www.w3.org/TR/cors/#author-request-headers)</span><span class="sxs-lookup"><span data-stu-id="c9f66-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="c9f66-264">이 규칙은 브라우저가 설정할 수 있는 헤더에 `User-Agent`적용되지 `Host`않습니다.(예: " 또는 `Content-Length`)</span><span class="sxs-lookup"><span data-stu-id="c9f66-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c9f66-265">다음은 이 문서의 [테스트 CORS](#testc) 섹션의 **[테스트 넣기]** 단추에서 만든 프리플라이트 요청과 유사한 예제 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="c9f66-266">프리플라이트 요청은 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="c9f66-267">여기에는 다음 헤더가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-267">It may include the following headers:</span></span>

* <span data-ttu-id="c9f66-268">[액세스 제어- 요청 - 메서드](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 실제 요청에 사용되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c9f66-269">[액세스 제어- 요청-헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): 앱이 실제 요청에 대해 설정하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c9f66-270">앞서 설명했듯이 브라우저에서 설정하는 헤더는 `User-Agent`없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="c9f66-271">액세스 제어 허용 방법</span><span class="sxs-lookup"><span data-stu-id="c9f66-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="c9f66-272">프리플라이트 요청이 거부되면 앱은 `200 OK` 응답을 반환하지만 CORS 헤더를 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="c9f66-273">따라서 브라우저는 원본 간 요청을 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="c9f66-274">거부된 프리플라이트 요청의 예는 이 문서의 [테스트 CORS](#testc) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="c9f66-275">F12 도구를 사용하여 콘솔 앱은 브라우저에 따라 다음 중 하나와 유사한 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="c9f66-276">파이어 폭스: 교차 원산지 요청 차단: 동일한 원본 정책에서 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`원격 리소스를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="c9f66-277">(이유: CORS 요청이 성공하지 못했습니다.)</span><span class="sxs-lookup"><span data-stu-id="c9f66-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="c9f66-278">자세히 알아보기</span><span class="sxs-lookup"><span data-stu-id="c9f66-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="c9f66-279">크롬 기반: CORShttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5정책에 의해 'origin'에서https://cors3.azurewebsites.net가져오기 에 대한 액세스: 사전 비행 요청에 대한 응답은 액세스 제어 검사를 통과하지 못합니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c9f66-280">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="c9f66-281">특정 헤더를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>다음을 호출하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="c9f66-282">모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 [호출합니다.](https://www.w3.org/TR/cors/#author-request-headers)</span><span class="sxs-lookup"><span data-stu-id="c9f66-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="c9f66-283">브라우저는 설정 `Access-Control-Request-Headers`하는 방법에 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c9f66-284">다음 중 하나일 경우</span><span class="sxs-lookup"><span data-stu-id="c9f66-284">If either:</span></span>

* <span data-ttu-id="c9f66-285">헤더는 다른 것으로 설정됩니다.`"*"`</span><span class="sxs-lookup"><span data-stu-id="c9f66-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="c9f66-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>호출됩니다: 지원하려는 `Accept` `Content-Type`사용자 `Origin`지정 헤더를 더한 추가 를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="c9f66-287">자동 프리플라이트 요청 코드</span><span class="sxs-lookup"><span data-stu-id="c9f66-287">Automatic preflight request code</span></span>

<span data-ttu-id="c9f66-288">CORS 정책이 적용되는 경우 다음 중 하나가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="c9f66-289">에서 호출하여 `app.UseCors` `Startup.Configure`전역적으로.</span><span class="sxs-lookup"><span data-stu-id="c9f66-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="c9f66-290">특성을 `[EnableCors]` 사용 하 여.</span><span class="sxs-lookup"><span data-stu-id="c9f66-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="c9f66-291">ASP.NET 코어는 프리플라이트 옵션 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="c9f66-292">현재 사용 하 여 엔드 포인트 `RequireCors` 단위로 CORS를 사용 하 여 자동 사전 비행 요청을 지원 ***하지*** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="c9f66-293">이 문서의 [테스트 CORS](#testc) 섹션에서는 이 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="c9f66-294">[HttpOptions] 프리플라이트 요청에 대한 속성</span><span class="sxs-lookup"><span data-stu-id="c9f66-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="c9f66-295">적절한 정책으로 CORS를 사용하도록 설정하면 ASP.NET Core는 일반적으로 CORS 프리플라이트 요청에 자동으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="c9f66-296">일부 시나리오에서는 그렇지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="c9f66-297">예를 들어 [끝점 라우팅과 함께 CORS를](#ecors)사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="c9f66-298">다음 코드는 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 특성을 사용하여 OPTIONS 요청에 대한 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="c9f66-299">앞의 코드 테스트에 대한 지침은 [끝점 라우팅이 포함된 테스트 CORS 및 [HttpOptions]를](#tcer) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c9f66-300">비행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-300">Set the preflight expiration time</span></span>

<span data-ttu-id="c9f66-301">헤더는 `Access-Control-Max-Age` 프리플라이트 요청에 대한 응답을 캐시할 수 있는 기간(기간)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c9f66-302">이 헤더를 설정하려면 다음을 호출하십시오. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*></span><span class="sxs-lookup"><span data-stu-id="c9f66-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c9f66-303">CORS 작동 방식</span><span class="sxs-lookup"><span data-stu-id="c9f66-303">How CORS works</span></span>

<span data-ttu-id="c9f66-304">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에서 발생하는 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c9f66-305">CORS는 보안 기능이 **아닙니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="c9f66-306">CORS는 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c9f66-307">예를 들어 악의적인 행위자(XSS)는 사이트에 대해 [XSS(교차 사이트 스크립팅)를](xref:security/cross-site-scripting) 사용하고 CORS 지원 사이트에 대한 교차 사이트 요청을 실행하여 정보를 도용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c9f66-308">CORS를 허용하면 API가 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="c9f66-309">CORS를 적용하는 것은 클라이언트(브라우저)의 결정입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c9f66-310">서버는 요청을 실행 하 고 응답을 반환, 오류를 반환 하 고 응답을 차단 하는 클라이언트입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c9f66-311">예를 들어 다음 도구 중 에는 서버 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c9f66-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c9f66-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c9f66-313">Postman</span><span class="sxs-lookup"><span data-stu-id="c9f66-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c9f66-314">.NET Http클라이언트</span><span class="sxs-lookup"><span data-stu-id="c9f66-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c9f66-315">주소 표시줄에 URL을 입력하여 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c9f66-316">서버가 브라우저가 금지될 수 있는 교차 원본 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행할 수 있도록 허용하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c9f66-317">CORS가 없는 브라우저는 원본 간 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="c9f66-318">CORS 이전에는 [JSONP를](https://www.w3schools.com/js/js_json_jsonp.asp) 사용하여 이러한 제한을 우회했습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c9f66-319">JSONP는 XHR을 `<script>` 사용하지 않고 태그를 사용하여 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c9f66-320">스크립트는 교차 원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c9f66-321">[CORS 사양은](https://www.w3.org/TR/cors/) 원본 간 요청을 가능하게 하는 몇 가지 새로운 HTTP 헤더를 도입했습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c9f66-322">브라우저가 CORS를 지원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c9f66-323">사용자 지정 자바스크립트 코드는 CORS를 사용하도록 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c9f66-324">배포된 [샘플의](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [PUT 테스트 단추](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="c9f66-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="c9f66-325">다음은 [값](https://cors3.azurewebsites.net/) 테스트 단추에서 에 `https://cors1.azurewebsites.net/api/values`대한 교차 원본 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="c9f66-326">헤더: `Origin`</span><span class="sxs-lookup"><span data-stu-id="c9f66-326">The `Origin` header:</span></span>

* <span data-ttu-id="c9f66-327">요청을 하는 사이트의 도메인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="c9f66-328">호스트와 달라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="c9f66-329">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="c9f66-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="c9f66-330">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="c9f66-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="c9f66-331">**헤더 요청**</span><span class="sxs-lookup"><span data-stu-id="c9f66-331">**Request headers**</span></span>

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

<span data-ttu-id="c9f66-332">요청에서 `OPTIONS` 서버는 응답에서 **응답 헤더 헤더를 설정합니다.** `Access-Control-Allow-Origin: {allowed origin}`</span><span class="sxs-lookup"><span data-stu-id="c9f66-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="c9f66-333">예를 들어 배포된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) 단추 `OPTIONS` 요청은 다음 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="c9f66-334">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="c9f66-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="c9f66-335">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="c9f66-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="c9f66-336">**헤더 요청**</span><span class="sxs-lookup"><span data-stu-id="c9f66-336">**Request headers**</span></span>

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

<span data-ttu-id="c9f66-337">앞의 **응답 헤더에서**서버는 응답에서 [액세스 제어-허용-원본](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="c9f66-338">이 `https://cors1.azurewebsites.net` 헤더의 값은 `Origin` 요청의 헤더와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="c9f66-339">이 호출된 경우 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 는 와일드카드 값이 반환됩니다. `Access-Control-Allow-Origin: *`</span><span class="sxs-lookup"><span data-stu-id="c9f66-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="c9f66-340">`AllowAnyOrigin`모든 원산지를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="c9f66-341">응답에 헤더가 `Access-Control-Allow-Origin` 포함되지 않으면 원본 간 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c9f66-342">특히 브라우저는 요청을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c9f66-343">서버가 성공적인 응답을 반환하더라도 브라우저는 클라이언트 앱에서 응답을 사용할 수 있도록 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="c9f66-344">옵션 요청 표시</span><span class="sxs-lookup"><span data-stu-id="c9f66-344">Display OPTIONS requests</span></span>

<span data-ttu-id="c9f66-345">기본적으로 Chrome 및 Edge 브라우저는 F12 도구의 네트워크 탭에 옵션 요청을 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="c9f66-346">이러한 브라우저에서 옵션 요청을 표시하려면 다음을 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="c9f66-347">`chrome://flags/#out-of-blink-cors` 또는 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="c9f66-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="c9f66-348">플래그를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-348">disable the flag.</span></span>
* <span data-ttu-id="c9f66-349">다시 시작할.</span><span class="sxs-lookup"><span data-stu-id="c9f66-349">restart.</span></span>

<span data-ttu-id="c9f66-350">파이어 폭스는 기본적으로 옵션 요청을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c9f66-351">IIS의 코르스</span><span class="sxs-lookup"><span data-stu-id="c9f66-351">CORS in IIS</span></span>

<span data-ttu-id="c9f66-352">IIS에 배포할 때 서버가 익명 액세스를 허용하도록 구성되지 않은 경우 Windows 인증 전에 CORS를 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c9f66-353">이 시나리오를 지원하려면 [IIS CORS 모듈을](https://www.iis.net/downloads/microsoft/iis-cors-module) 앱에 대해 설치하고 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="c9f66-354">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="c9f66-354">Test CORS</span></span>

<span data-ttu-id="c9f66-355">[샘플 다운로드에는](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) CORS를 테스트하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="c9f66-356">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c9f66-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="c9f66-357">샘플은 Razor 페이지가 추가된 API 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="c9f66-358">`WithOrigins("https://localhost:<port>");`다운로드 샘플 [코드와](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)유사한 샘플 앱을 테스트하는 데만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="c9f66-359">다음은 `ValuesController` 테스트를 위한 끝점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="c9f66-360">[MyDisplayRouteInfo는](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 패키지에서 제공되며 경로 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="c9f66-361">다음 방법 중 하나를 사용하여 위의 샘플 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="c9f66-362">에서 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)배포된 샘플 앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="c9f66-363">샘플을 다운로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="c9f66-364">의 기본 `dotnet run` URL을 사용하여 `https://localhost:5001`샘플을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="c9f66-365">의 URL에 대 한 44398로 설정 된 `https://localhost:44398`포트와 Visual Studio에서 샘플을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="c9f66-366">F12 도구가 있는 브라우저 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="c9f66-367">**값** 단추를 선택하고 **네트워크** 탭에서 헤더를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="c9f66-368">PUT **테스트** 버튼을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-368">Select the **PUT test** button.</span></span> <span data-ttu-id="c9f66-369">[옵션 요청을](#options) 표시하는 방법에 대한 지침에 대한 옵션 표시 요청을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="c9f66-370">**PUT 테스트는** 옵션 프리플라이트 요청과 PUT 요청의 두 가지 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="c9f66-371">**`GetValues2 [DisableCors]`** 실패한 CORS 요청을 트리거하려면 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="c9f66-372">문서에서 설명한 대로 응답은 200개의 성공을 반환하지만 CORS 요청은 이루어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="c9f66-373">**콘솔** 탭을 선택하여 CORS 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="c9f66-374">브라우저에 따라 다음과 유사한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="c9f66-375">`'https://cors1.azurewebsites.net/api/values/GetValues2'` 원본에서 `'https://cors3.azurewebsites.net'` 가져오기에 대한 액세스가 CORS 정책에 의해 차단되었습니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="c9f66-376">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="c9f66-377">CORS 지원 끝점은 [컬,](https://curl.haxx.se/) [피들러](https://www.telerik.com/fiddler)또는 [우체부와](https://www.getpostman.com/)같은 도구를 사용하여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c9f66-378">도구를 사용하는 경우 `Origin` 헤더가 지정한 요청의 출처는 요청을 받는 호스트와 달라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c9f66-379">요청이 헤더 값에 따라 원본 간 이아닌 경우: *cross-origin* `Origin`</span><span class="sxs-lookup"><span data-stu-id="c9f66-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c9f66-380">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c9f66-381">CORS 헤더는 응답에서 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="c9f66-382">다음 명령은 `curl` 정보와 함께 OPTIONS 요청을 발행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="c9f66-383">끝점 라우팅 및 [HttpOptions]를 사용 하 고 CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="c9f66-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="c9f66-384">현재 를 사용하여 `RequireCors` 엔드포인트별로 CORS를 사용하도록 설정하는 것은 자동 [프리플라이트 요청을](#apf)지원하지 ***않습니다.***</span><span class="sxs-lookup"><span data-stu-id="c9f66-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="c9f66-385">[CORS를 활성화하기 위해 끝점 라우팅을](#ecors)사용하는 다음 코드를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="c9f66-386">다음은 `TodoItems1Controller` 테스트를 위한 끝점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="c9f66-387">배포된 [샘플의](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [테스트 페이지에서](https://cors1.azurewebsites.net/test?number=1) 이전 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="c9f66-388">끝점이 프리플라이트 요청에 `[EnableCors]` 응답하고 응답하기 때문에 삭제 **[EnableCors]** 및 GET **[EnableCors] 버튼이** 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="c9f66-389">다른 끝점이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-389">The other endpoints fails.</span></span> <span data-ttu-id="c9f66-390">[JavaScript가](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 다음을 전송하기 때문에 **GET** 버튼이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="c9f66-391">다음은 `TodoItems2Controller` 유사한 끝점을 제공하지만 OPTIONS 요청에 응답하는 명시적 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="c9f66-392">배포된 샘플의 [테스트 페이지에서](https://cors1.azurewebsites.net/test?number=2) 이전 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="c9f66-393">**컨트롤러** 드롭다운 목록에서 **프리플라이트** 를 선택한 다음 **컨트롤러 를 설정합니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="c9f66-394">끝점에 대한 모든 `TodoItems2Controller` CORS 호출이 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9f66-395">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c9f66-395">Additional resources</span></span>

* [<span data-ttu-id="c9f66-396">CORS(크로스-원본 자원 공유)</span><span class="sxs-lookup"><span data-stu-id="c9f66-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c9f66-397">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="c9f66-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9f66-398">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c9f66-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c9f66-399">이 문서에서는 ASP.NET 코어 앱에서 CORS를 사용하도록 설정하는 방법을 보여 주며, CORS를 활성화하는 방법을 보여 주시고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="c9f66-400">브라우저 보안은 웹 페이지가 웹 페이지를 제공한 도메인과 다른 도메인에 대한 요청을 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="c9f66-401">이 제한을 *동일한 원본 정책이라고*합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c9f66-402">동일한 원본 정책은 악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c9f66-403">경우에 따라 다른 사이트에서 앱에 대한 원본 간 요청을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="c9f66-404">자세한 내용은 [모질라 CORS 문서를](https://developer.mozilla.org/docs/Web/HTTP/CORS)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="c9f66-405">[원본 간 리소스](https://www.w3.org/TR/cors/) 공유(CORS):</span><span class="sxs-lookup"><span data-stu-id="c9f66-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="c9f66-406">서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="c9f66-407">보안 기능이 **아니며** CORS는 보안을 완화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="c9f66-408">CORS를 허용하면 API가 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="c9f66-409">자세한 내용은 [CORS 작동 방식을](#how-cors)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="c9f66-410">서버가 다른 요청을 거부하면서 일부 원본 간 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="c9f66-411">[JSONP와](/dotnet/framework/wcf/samples/jsonp)같은 이전 기술보다 더 안전하고 유연합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="c9f66-412">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9f66-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="c9f66-413">동일한 원산지</span><span class="sxs-lookup"><span data-stu-id="c9f66-413">Same origin</span></span>

<span data-ttu-id="c9f66-414">두 URL은 동일한 구성표, 호스트 및[포트(RFC 6454)가](https://tools.ietf.org/html/rfc6454)있는 경우 동일한 출처를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="c9f66-415">이 두 URL의 출처는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="c9f66-416">이러한 URL의 출처는 이전 두 URL과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="c9f66-417">`https://example.net`&ndash; 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="c9f66-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="c9f66-418">`https://www.example.com/foo.html`&ndash; 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="c9f66-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="c9f66-419">`http://example.com/foo.html`&ndash; 다른 구성표</span><span class="sxs-lookup"><span data-stu-id="c9f66-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="c9f66-420">`https://example.com:9000/foo.html`&ndash; 다른 포트</span><span class="sxs-lookup"><span data-stu-id="c9f66-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="c9f66-421">인터넷 익스플로러는 출처를 비교할 때 포트를 고려하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="c9f66-422">명명된 정책 및 미들웨어가 있는 CORS</span><span class="sxs-lookup"><span data-stu-id="c9f66-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="c9f66-423">CORS 미들웨어는 교차 원산지 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="c9f66-424">다음 코드는 지정된 원본을 가진 전체 앱에 대한 CORS를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="c9f66-425">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="c9f66-425">The preceding code:</span></span>

* <span data-ttu-id="c9f66-426">정책 이름을 "myAllowSpecificOrigins"로\_설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="c9f66-427">정책 이름은 임의입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="c9f66-428">CORS를 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 활성화하는 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="c9f66-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식으로](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="c9f66-430">람다는 개체를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 취합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="c9f66-431">[과](#cors-policy-options)같은 `WithOrigins`구성 옵션은 이 문서의 후반부에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="c9f66-432">메서드 <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 호출은 앱의 서비스 컨테이너에 CORS 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="c9f66-433">자세한 내용은 이 문서의 [CORS 정책 옵션을](#cpo) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="c9f66-434">메서드는 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="c9f66-435">참고: URL에 후행 슬래시()가`/` **포함되어서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="c9f66-436">URL이 `/`으로 종료되면 비교가 `false` 반환되고 헤더가 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="c9f66-437">다음 코드는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="c9f66-438">참고: `UseCors` 먼저 `UseMvc`호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="c9f66-439">[Razor 페이지, 컨트롤러 및 작업 메서드에서 CORS 활성화를](#ecors) 참조하여 페이지/컨트롤러/작업 수준에서 CORS 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="c9f66-440">위의 코드와 유사한 코드 테스트에 대한 지침은 [CORS 테스트를](#test) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="c9f66-441">특성을 사용하여 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="c9f66-441">Enable CORS with attributes</span></span>

<span data-ttu-id="c9f66-442">[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용하는 대안을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="c9f66-443">이 `[EnableCors]` 특성을 사용하면 모든 끝점이 아니라 선택한 끝점에 대한 CORS를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="c9f66-444">기본 `[EnableCors]` 정책을 지정하고 `[EnableCors("{Policy String}")]` 정책을 지정하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="c9f66-445">특성을 `[EnableCors]` 다음에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="c9f66-446">면도기 페이지`PageModel`</span><span class="sxs-lookup"><span data-stu-id="c9f66-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="c9f66-447">Controller</span><span class="sxs-lookup"><span data-stu-id="c9f66-447">Controller</span></span>
* <span data-ttu-id="c9f66-448">컨트롤러 작업 메서드</span><span class="sxs-lookup"><span data-stu-id="c9f66-448">Controller action method</span></span>

<span data-ttu-id="c9f66-449">특성을 사용하여 컨트롤러/페이지 모델/작업에 다른 `[EnableCors]` 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="c9f66-450">특성이 `[EnableCors]` 컨트롤러/페이지 모델/작업 메서드에 적용되고 중간 웨어에서 CORS가 활성화되면 두 정책이 ***모두*** 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="c9f66-451">정책을 ***결합하지 않는*** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="c9f66-452">속성 `[EnableCors]` 또는 미들웨어를 사용 하 여 \***둘 다 아닙니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="c9f66-453">을 `[EnableCors]`사용하는 경우 기본 정책을 **정의하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="c9f66-454">다음 코드는 각 메서드에 다른 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="c9f66-455">다음 코드는 CORS 기본 정책과 다음 `"AnotherPolicy"`이라는 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="c9f66-456">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="c9f66-456">Disable CORS</span></span>

<span data-ttu-id="c9f66-457">[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지 모델/작업에 대한 CORS를 비활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="c9f66-458">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="c9f66-458">CORS policy options</span></span>

<span data-ttu-id="c9f66-459">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양한 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="c9f66-460">허용된 원원 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="c9f66-461">허용되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="c9f66-462">허용된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="c9f66-463">노출된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="c9f66-464">원본 간 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="c9f66-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="c9f66-465">비행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="c9f66-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>에서 `Startup.ConfigureServices`호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c9f66-467">일부 옵션의 경우 [먼저 CORS 작동 방식](#how-cors) 섹션을 읽는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c9f66-468">허용된 원원 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-468">Set the allowed origins</span></span>

<span data-ttu-id="c9f66-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; 모든 구성표(또는)를`http` `https`사용하여 모든 원본의 CORS 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="c9f66-470">`AllowAnyOrigin`모든 웹 *사이트가* 앱에 대한 교차 원산지 요청을 할 수 있기 때문에 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="c9f66-471">`AllowAnyOrigin` 지정하고 `AllowCredentials` 안전하지 않은 구성이며 사이트 간 요청이 위조될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="c9f66-472">보안 앱의 경우 클라이언트가 서버 리소스에 액세스하도록 권한을 부여해야 하는 경우 정확한 원본 목록을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="c9f66-473">`AllowAnyOrigin`비행 전 요청 `Access-Control-Allow-Origin` 및 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="c9f66-474">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9f66-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; 오리진이 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 허용되는지 평가할 때 원본이 구성된 와일드카드 도메인과 일치하도록 허용하는 함수로 정책의 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c9f66-476">허용되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c9f66-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="c9f66-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="c9f66-478">HTTP 메서드를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="c9f66-479">비행 전 요청 `Access-Control-Allow-Methods` 및 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="c9f66-480">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c9f66-481">허용된 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-481">Set the allowed request headers</span></span>

<span data-ttu-id="c9f66-482">*작성자 요청 헤더라고*하는 CORS 요청에서 특정 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 보낼 수 있도록 하려면 허용된 헤더를 호출하고 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c9f66-483">모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c9f66-484">이 설정은 프리플라이트 `Access-Control-Request-Headers` 요청 및 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="c9f66-485">자세한 내용은 [프리플라이트 요청](#preflight-requests) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="c9f66-486">CORS 미들웨어는 항상 CorsPolicy.Headers에서 구성된 값에 `Access-Control-Request-Headers` 관계없이 4개의 헤더를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="c9f66-487">이 헤더 목록에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="c9f66-488">예를 들어 다음과 같이 구성된 앱을 고려해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="c9f66-489">CORS 미들웨어는 항상 화이트리스트가 있기 때문에 `Content-Language` 다음 요청 헤더를 사용하여 프리플라이트 요청에 성공적으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="c9f66-490">노출된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-490">Set the exposed response headers</span></span>

<span data-ttu-id="c9f66-491">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="c9f66-492">자세한 내용은 [W3C 교차 원본 리소스 공유(용어): 간단한 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="c9f66-493">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="c9f66-494">CORS 사양은 이러한 *헤더간단한 응답 헤더를 호출합니다.*</span><span class="sxs-lookup"><span data-stu-id="c9f66-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="c9f66-495">앱에서 다른 헤더를 사용할 수 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>있도록 하려면 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="c9f66-496">원본 간 요청의 자격 증명</span><span class="sxs-lookup"><span data-stu-id="c9f66-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="c9f66-497">자격 증명에는 CORS 요청에서 특별한 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c9f66-498">기본적으로 브라우저는 원본 간 요청이 있는 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="c9f66-499">자격 증명에는 쿠키 및 HTTP 인증 스키마가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="c9f66-500">원본 간 요청이 있는 자격 증명을 보내려면 `true`클라이언트를 로 설정해야 `XMLHttpRequest.withCredentials` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="c9f66-501">직접 `XMLHttpRequest` 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="c9f66-502">jQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="c9f66-503">가져오기 [API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용 :</span><span class="sxs-lookup"><span data-stu-id="c9f66-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="c9f66-504">서버는 자격 증명을 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-504">The server must allow the credentials.</span></span> <span data-ttu-id="c9f66-505">원본 간 자격 증명을 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>허용하려면 다음을 호출하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="c9f66-506">HTTP 응답에는 `Access-Control-Allow-Credentials` 서버가 원본 간 요청에 대한 자격 증명을 허용한다는 것을 브라우저에 알리는 헤더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c9f66-507">브라우저가 자격 증명을 전송하지만 응답에 유효한 `Access-Control-Allow-Credentials` 헤더가 포함되지 않으면 브라우저는 앱에 대한 응답을 노출하지 않으며 원본 간 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="c9f66-508">원본 간 자격 증명을 허용하는 것은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="c9f66-509">다른 도메인의 웹 사이트는 사용자가 모르는 새앱 한 사용자의 자격 증명을 사용자 없이 사용자를 대신하여 앱으로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="c9f66-510">또한 CORS 사양에 `"*"` `Access-Control-Allow-Credentials` 따르면 헤더가 있는 경우 (모든 원본)에 대한 설정이 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="c9f66-511">비행 전 요청</span><span class="sxs-lookup"><span data-stu-id="c9f66-511">Preflight requests</span></span>

<span data-ttu-id="c9f66-512">일부 CORS 요청의 경우 브라우저는 실제 요청을 하기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="c9f66-513">이 요청을 *프리플라이트 요청이라고*합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="c9f66-514">브라우저는 다음과 같은 조건이 true인 경우 프리플라이트 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="c9f66-515">요청 방법은 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="c9f66-516">앱에서 요청 `Accept`헤더를 " `Accept-Language` `Content-Language` `Content-Type`또는 `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="c9f66-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="c9f66-517">헤더(설정된 `Content-Type` 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="c9f66-518">클라이언트 요청에 대해 설정된 요청 헤더에 대한 규칙은 앱이 `setRequestHeader` `XMLHttpRequest` 개체를 호출하여 설정하는 헤더에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="c9f66-519">CORS 사양은 이러한 헤더 *작성자 요청 헤더를 호출합니다.*</span><span class="sxs-lookup"><span data-stu-id="c9f66-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="c9f66-520">이 규칙은 브라우저가 설정할 수 있는 헤더에 `User-Agent`적용되지 `Host`않습니다.(예: " 또는 `Content-Length`)</span><span class="sxs-lookup"><span data-stu-id="c9f66-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="c9f66-521">다음은 프리플라이트 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="c9f66-522">비행 전 요청은 HTTP OPTIONS 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="c9f66-523">여기에는 두 개의 특수 헤더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-523">It includes two special headers:</span></span>

* <span data-ttu-id="c9f66-524">`Access-Control-Request-Method`: 실제 요청에 사용할 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="c9f66-525">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="c9f66-526">앞서 설명했듯이 브라우저에서 설정하는 헤더는 `User-Agent`없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="c9f66-527">적절한 정책으로 CORS를 사용하도록 설정하면 ASP.NET Core는 일반적으로 CORS 프리플라이트 요청에 자동으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="c9f66-528">[비행 전 요청에 대한 [HttpOptions] 특성을](#pro)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="c9f66-529">CORS 프리플라이트 요청에는 `Access-Control-Request-Headers` 실제 요청과 함께 전송되는 헤더를 서버에 나타내는 헤더가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="c9f66-530">특정 헤더를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>다음을 호출하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="c9f66-531">모든 작성자 요청 헤더를 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>허용하려면 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="c9f66-532">브라우저는 설정 `Access-Control-Request-Headers`하는 방법에 완전히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="c9f66-533">헤더를 (또는 `"*"` 사용) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>이외의 것으로 설정하는 경우 최소한 `Accept` `Content-Type`을 `Origin`포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="c9f66-534">다음은 사전 비행 요청에 대한 예제 응답입니다(서버가 요청을 허용한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="c9f66-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="c9f66-535">응답에는 허용된 메서드를 `Access-Control-Allow-Methods` 나열하는 `Access-Control-Allow-Headers` 헤더와 허용된 헤더를 나열하는 헤더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="c9f66-536">프리플라이트 요청이 성공하면 브라우저는 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="c9f66-537">프리플라이트 요청이 거부되면 앱은 *200 확인* 응답을 반환하지만 CORS 헤더를 다시 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="c9f66-538">따라서 브라우저는 원본 간 요청을 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="c9f66-539">비행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="c9f66-539">Set the preflight expiration time</span></span>

<span data-ttu-id="c9f66-540">헤더는 `Access-Control-Max-Age` 프리플라이트 요청에 대한 응답을 캐시할 수 있는 기간(기간)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="c9f66-541">이 헤더를 설정하려면 다음을 호출하십시오. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*></span><span class="sxs-lookup"><span data-stu-id="c9f66-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="c9f66-542">CORS 작동 방식</span><span class="sxs-lookup"><span data-stu-id="c9f66-542">How CORS works</span></span>

<span data-ttu-id="c9f66-543">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에서 발생하는 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="c9f66-544">CORS는 보안 기능이 **아닙니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="c9f66-545">CORS는 서버가 동일한 원본 정책을 완화할 수 있는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="c9f66-546">예를 들어 악의적인 행위자(XSS)는 사이트에 대해 [XSS 간 간 스크립팅 방지를](xref:security/cross-site-scripting) 사용하고 CORS 지원 사이트에 대한 교차 사이트 요청을 실행하여 정보를 도용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="c9f66-547">CORS를 허용하면 API가 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="c9f66-548">CORS를 적용하는 것은 클라이언트(브라우저)의 결정입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="c9f66-549">서버는 요청을 실행 하 고 응답을 반환, 오류를 반환 하 고 응답을 차단 하는 클라이언트입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="c9f66-550">예를 들어 다음 도구 중 에는 서버 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="c9f66-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="c9f66-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="c9f66-552">Postman</span><span class="sxs-lookup"><span data-stu-id="c9f66-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="c9f66-553">.NET Http클라이언트</span><span class="sxs-lookup"><span data-stu-id="c9f66-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="c9f66-554">주소 표시줄에 URL을 입력하여 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="c9f66-555">서버가 브라우저가 금지될 수 있는 교차 원본 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행할 수 있도록 허용하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="c9f66-556">CORS가 없는 브라우저는 원본 간 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="c9f66-557">CORS 이전에는 [JSONP를](https://www.w3schools.com/js/js_json_jsonp.asp) 사용하여 이러한 제한을 우회했습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="c9f66-558">JSONP는 XHR을 `<script>` 사용하지 않고 태그를 사용하여 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="c9f66-559">스크립트는 교차 원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="c9f66-560">[CORS 사양은](https://www.w3.org/TR/cors/) 원본 간 요청을 가능하게 하는 몇 가지 새로운 HTTP 헤더를 도입했습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c9f66-561">브라우저가 CORS를 지원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="c9f66-562">사용자 지정 자바스크립트 코드는 CORS를 사용하도록 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="c9f66-563">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="c9f66-564">헤더는 `Origin` 요청을 하는 사이트의 도메인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="c9f66-565">`Origin` 헤더는 필수이며 호스트와 달라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="c9f66-566">서버가 요청을 허용하면 응답에서 `Access-Control-Allow-Origin` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="c9f66-567">이 헤더의 값은 `Origin` 요청의 헤더와 일치하거나 와일드카드 `"*"`값으로 모든 원본이 허용됨을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="c9f66-568">응답에 헤더가 `Access-Control-Allow-Origin` 포함되지 않으면 원본 간 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="c9f66-569">특히 브라우저는 요청을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c9f66-570">서버가 성공적인 응답을 반환하더라도 브라우저는 클라이언트 앱에서 응답을 사용할 수 있도록 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="c9f66-571">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="c9f66-571">Test CORS</span></span>

<span data-ttu-id="c9f66-572">CORS를 테스트하려면 다음을 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="c9f66-572">To test CORS:</span></span>

1. <span data-ttu-id="c9f66-573">[API 프로젝트를 만듭니다.](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="c9f66-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="c9f66-574">또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="c9f66-575">이 문서의 방법 중 하나를 사용하여 CORS를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="c9f66-576">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="c9f66-577">`WithOrigins("https://localhost:<port>");`다운로드 샘플 [코드와](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)유사한 샘플 앱을 테스트하는 데만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="c9f66-578">웹 앱 프로젝트(Razor 페이지 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="c9f66-579">이 샘플에서는 면도기 페이지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="c9f66-580">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="c9f66-581">*Index.cshtml* 파일에 다음 강조 표시된 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="c9f66-582">앞의 코드에서 배포된 앱의 URL로 바꿉니다. `url: 'https://<web app>.azurewebsites.net/api/values/1',`</span><span class="sxs-lookup"><span data-stu-id="c9f66-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="c9f66-583">API 프로젝트를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-583">Deploy the API project.</span></span> <span data-ttu-id="c9f66-584">예를 들어 [Azure 에 배포합니다.](xref:host-and-deploy/azure-apps/index)</span><span class="sxs-lookup"><span data-stu-id="c9f66-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="c9f66-585">바탕 화면에서 Razor 페이지 또는 MVC 앱을 실행하고 **테스트** 버튼을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="c9f66-586">F12 도구를 사용하여 오류 메시지를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="c9f66-587">로컬 호스트 원본을 `WithOrigins` 제거하고 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="c9f66-588">또는 다른 포트를 통해 클라이언트 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="c9f66-589">예를 들어 Visual Studio에서 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="c9f66-590">클라이언트 앱으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-590">Test with the client app.</span></span> <span data-ttu-id="c9f66-591">CORS 오류는 오류를 반환하지만 오류 메시지는 JavaScript에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="c9f66-592">F12 도구의 콘솔 탭을 사용하여 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="c9f66-593">브라우저에 따라 다음과 유사한 오류가 발생합니다(F12 도구 콘솔에서).</span><span class="sxs-lookup"><span data-stu-id="c9f66-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="c9f66-594">마이크로 소프트 에지 사용 :</span><span class="sxs-lookup"><span data-stu-id="c9f66-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="c9f66-595">**SEC7120: [CORS] `https://localhost:44375` 원본에서 `https://localhost:44375` 원본간 리소스에 대한 액세스 제어-허용-원본 원본 응답 헤더에서 원본을 찾지 못했습니다.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="c9f66-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="c9f66-596">크롬 사용:</span><span class="sxs-lookup"><span data-stu-id="c9f66-596">Using Chrome:</span></span>

     <span data-ttu-id="c9f66-597">**원본에서 `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` XMLHttpRequest에 대한 액세스가 CORS 정책에 의해 차단되었습니다: 요청된 리소스에 '액세스 제어-허용-원본' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="c9f66-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="c9f66-598">CORS 지원 엔드포인트는 [Fiddler](https://www.telerik.com/fiddler) 또는 [Postman](https://www.getpostman.com/)과 같은 도구를 사용하여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="c9f66-599">도구를 사용하는 경우 `Origin` 헤더가 지정한 요청의 출처는 요청을 받는 호스트와 달라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="c9f66-600">요청이 헤더 값에 따라 원본 간 이아닌 경우: *cross-origin* `Origin`</span><span class="sxs-lookup"><span data-stu-id="c9f66-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="c9f66-601">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="c9f66-602">CORS 헤더는 응답에서 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="c9f66-603">IIS의 코르스</span><span class="sxs-lookup"><span data-stu-id="c9f66-603">CORS in IIS</span></span>

<span data-ttu-id="c9f66-604">IIS에 배포할 때 서버가 익명 액세스를 허용하도록 구성되지 않은 경우 Windows 인증 전에 CORS를 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="c9f66-605">이 시나리오를 지원하려면 [IIS CORS 모듈을](https://www.iis.net/downloads/microsoft/iis-cors-module) 앱에 대해 설치하고 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c9f66-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9f66-606">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c9f66-606">Additional resources</span></span>

* [<span data-ttu-id="c9f66-607">CORS(크로스-원본 자원 공유)</span><span class="sxs-lookup"><span data-stu-id="c9f66-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="c9f66-608">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="c9f66-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
