---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/index
ms.openlocfilehash: 7b0212ce6463d00a4c5cc87e2b36e1e7e7c5a54e
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445413"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="374a0-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="374a0-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="374a0-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="374a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="374a0-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="374a0-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-106">Each component:</span></span>

* <span data-ttu-id="374a0-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="374a0-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="374a0-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="374a0-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="374a0-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="374a0-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="374a0-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="374a0-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="374a0-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="374a0-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="374a0-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="374a0-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="374a0-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="374a0-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="374a0-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="374a0-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="374a0-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="374a0-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="374a0-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="374a0-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="374a0-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="374a0-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="374a0-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="374a0-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="374a0-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="374a0-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="374a0-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="374a0-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="374a0-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="374a0-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="374a0-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="374a0-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="374a0-140">예를 들어 [헤더 및 상태 코드를 설정하면 예외를 throw합니다](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span><span class="sxs-lookup"><span data-stu-id="374a0-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="374a0-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="374a0-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-142">May cause a protocol violation.</span></span> <span data-ttu-id="374a0-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="374a0-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-144">May corrupt the body format.</span></span> <span data-ttu-id="374a0-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="374a0-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="374a0-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="374a0-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="374a0-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-149">`Run` is a convention.</span></span> <span data-ttu-id="374a0-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="374a0-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="374a0-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="374a0-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="374a0-153">Middleware order</span></span>

<span data-ttu-id="374a0-154">다음 다이어그램은 ASP.NET Core MVC 및 Razor Pages 앱의 전체 요청 처리 파이프라인을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="374a0-155">일반적인 앱에서 기존 미들웨어의 순서가 지정되는 방식과 사용자 지정 미들웨어가 추가되는 위치를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="374a0-156">사용자는 원하는 대로 기존 미들웨어의 순서를 바꾸거나 시나리오의 필요에 따라 새 사용자 지정 미들웨어를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 미들웨어 파이프라인](index/_static/middleware-pipeline.svg)

<span data-ttu-id="374a0-158">앞에 나온 다이어그램의 **엔드포인트** 미들웨어는 해당 앱 형식&mdash;MVC 또는 Razor Pages에 따라 필터 파이프라인을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 필터 파이프라인](index/_static/mvc-endpoint.svg)

<span data-ttu-id="374a0-160">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="374a0-161">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="374a0-162">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="374a0-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="374a0-163">In the preceding code:</span></span>

* <span data-ttu-id="374a0-164">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="374a0-165">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="374a0-166">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="374a0-166">For example:</span></span>
  * <span data-ttu-id="374a0-167">`UseCors`, `UseAuthentication`, `UseAuthorization`은 표시된 순서를 지켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="374a0-168">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="374a0-169">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="374a0-170">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="374a0-170">Exception/error handling</span></span>
   * <span data-ttu-id="374a0-171">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="374a0-172">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="374a0-173">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="374a0-174">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="374a0-175">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="374a0-176">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="374a0-177">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="374a0-178">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="374a0-179">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-179">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="374a0-180">요청을 라우팅하도록 미들웨어(<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="374a0-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="374a0-181">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="374a0-182">권한 부여 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="374a0-183">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="374a0-184">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-184">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="374a0-185">엔드포인트 라우팅 미들웨어(<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>를 포함하는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="374a0-186">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="374a0-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="374a0-188">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="374a0-189">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="374a0-190">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="374a0-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="374a0-191">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-191">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="374a0-192">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="374a0-193">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="374a0-194">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="374a0-195">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="374a0-196">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="374a0-197">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="374a0-198">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-198">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="374a0-199">SPA(단일 페이지 애플리케이션)의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="374a0-200">SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="374a0-201">다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="374a0-202">클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="374a0-203">SPA에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref:spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="374a0-204">전달된 헤더 미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="374a0-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="374a0-205">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="374a0-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="374a0-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="374a0-207">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="374a0-208">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="374a0-209">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="374a0-210">요청</span><span class="sxs-lookup"><span data-stu-id="374a0-210">Request</span></span>             | <span data-ttu-id="374a0-211">응답</span><span class="sxs-lookup"><span data-stu-id="374a0-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="374a0-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="374a0-212">localhost:1234</span></span>      | <span data-ttu-id="374a0-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="374a0-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="374a0-214">localhost:1234/map1</span></span> | <span data-ttu-id="374a0-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="374a0-215">Map Test 1</span></span>                   |
| <span data-ttu-id="374a0-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="374a0-216">localhost:1234/map2</span></span> | <span data-ttu-id="374a0-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="374a0-217">Map Test 2</span></span>                   |
| <span data-ttu-id="374a0-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="374a0-218">localhost:1234/map3</span></span> | <span data-ttu-id="374a0-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="374a0-220">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="374a0-221">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-221">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="374a0-222">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="374a0-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="374a0-224">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="374a0-225">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="374a0-226">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="374a0-227">요청</span><span class="sxs-lookup"><span data-stu-id="374a0-227">Request</span></span>                       | <span data-ttu-id="374a0-228">응답</span><span class="sxs-lookup"><span data-stu-id="374a0-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="374a0-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="374a0-229">localhost:1234</span></span>                | <span data-ttu-id="374a0-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="374a0-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="374a0-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="374a0-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="374a0-232">Branch used = master</span></span>         |

<span data-ttu-id="374a0-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="374a0-234">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하지 않는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="374a0-235">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="374a0-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="374a0-236">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-236">is written for all requests.</span></span> <span data-ttu-id="374a0-237">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="374a0-238">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="374a0-238">Built-in middleware</span></span>

<span data-ttu-id="374a0-239">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="374a0-240">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="374a0-241">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="374a0-242">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="374a0-243">미들웨어</span><span class="sxs-lookup"><span data-stu-id="374a0-243">Middleware</span></span> | <span data-ttu-id="374a0-244">설명</span><span class="sxs-lookup"><span data-stu-id="374a0-244">Description</span></span> | <span data-ttu-id="374a0-245">순서</span><span class="sxs-lookup"><span data-stu-id="374a0-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="374a0-246">인증</span><span class="sxs-lookup"><span data-stu-id="374a0-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="374a0-247">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-247">Provides authentication support.</span></span> | <span data-ttu-id="374a0-248">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="374a0-249">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="374a0-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="374a0-250">권한 부여</span><span class="sxs-lookup"><span data-stu-id="374a0-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="374a0-251">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-251">Provides authorization support.</span></span> | <span data-ttu-id="374a0-252">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="374a0-253">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="374a0-253">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="374a0-254">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-254">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="374a0-255">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-255">Before middleware that issues cookies.</span></span> <span data-ttu-id="374a0-256">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="374a0-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="374a0-257">CORS</span><span class="sxs-lookup"><span data-stu-id="374a0-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="374a0-258">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="374a0-259">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-259">Before components that use CORS.</span></span> <span data-ttu-id="374a0-260">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="374a0-261">진단</span><span class="sxs-lookup"><span data-stu-id="374a0-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="374a0-262">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="374a0-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="374a0-263">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-263">Before components that generate errors.</span></span> <span data-ttu-id="374a0-264">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="374a0-265">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="374a0-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="374a0-266">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="374a0-267">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="374a0-268">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="374a0-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="374a0-269">상태 검사</span><span class="sxs-lookup"><span data-stu-id="374a0-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="374a0-270">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="374a0-271">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="374a0-272">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="374a0-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="374a0-273">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="374a0-274">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="374a0-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="374a0-275">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="374a0-276">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="374a0-277">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="374a0-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="374a0-278">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="374a0-279">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="374a0-280">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="374a0-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="374a0-281">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="374a0-282">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="374a0-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="374a0-283">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="374a0-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="374a0-284">MVC</span><span class="sxs-lookup"><span data-stu-id="374a0-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="374a0-285">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-285">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="374a0-286">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="374a0-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="374a0-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="374a0-288">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="374a0-289">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="374a0-290">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="374a0-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="374a0-291">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-291">Provides support for caching responses.</span></span> | <span data-ttu-id="374a0-292">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-292">Before components that require caching.</span></span> <span data-ttu-id="374a0-293">`UseCORS`는 `UseResponseCaching` 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="374a0-294">응답 압축</span><span class="sxs-lookup"><span data-stu-id="374a0-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="374a0-295">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="374a0-296">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="374a0-297">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="374a0-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="374a0-298">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-298">Provides localization support.</span></span> | <span data-ttu-id="374a0-299">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="374a0-300">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="374a0-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="374a0-301">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="374a0-302">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="374a0-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="374a0-303">SPA</span><span class="sxs-lookup"><span data-stu-id="374a0-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="374a0-304">SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="374a0-305">정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="374a0-306">세션</span><span class="sxs-lookup"><span data-stu-id="374a0-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="374a0-307">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="374a0-308">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="374a0-309">정적 파일</span><span class="sxs-lookup"><span data-stu-id="374a0-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="374a0-310">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="374a0-311">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="374a0-312">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="374a0-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="374a0-313">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="374a0-314">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="374a0-315">WebSockets</span><span class="sxs-lookup"><span data-stu-id="374a0-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="374a0-316">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="374a0-317">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="374a0-318">추가 자료</span><span class="sxs-lookup"><span data-stu-id="374a0-318">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="374a0-319">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="374a0-319">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="374a0-320">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-320">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="374a0-321">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-321">Each component:</span></span>

* <span data-ttu-id="374a0-322">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-322">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="374a0-323">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-323">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="374a0-324">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-324">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="374a0-325">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-325">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="374a0-326">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-326">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="374a0-327">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-327">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="374a0-328">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어*라고 하며, *미들웨어 구성 요소*라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-328">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="374a0-329">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-329">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="374a0-330">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-330">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="374a0-331"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-331"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="374a0-332">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="374a0-332">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="374a0-333">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-333">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="374a0-334">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-334">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="374a0-335">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-335">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="374a0-339">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-339">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="374a0-340">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-340">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="374a0-341">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-341">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="374a0-342">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-342">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="374a0-343">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-343">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="374a0-344">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-344">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="374a0-345"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-345">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="374a0-346">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-346">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="374a0-347">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-347">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="374a0-348">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-348">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="374a0-349">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-349">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="374a0-350">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-350">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="374a0-351">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어*로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-351">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="374a0-352">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-352">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="374a0-353">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-353">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="374a0-354">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="374a0-354">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="374a0-355">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-355">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="374a0-356">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-356">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="374a0-357">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-357">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="374a0-358">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-358">May cause a protocol violation.</span></span> <span data-ttu-id="374a0-359">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-359">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="374a0-360">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-360">May corrupt the body format.</span></span> <span data-ttu-id="374a0-361">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-361">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="374a0-362"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-362"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="374a0-363">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="374a0-363">Middleware order</span></span>

<span data-ttu-id="374a0-364">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-364">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="374a0-365">순서는 보안, 성능 및 기능에 **중요**합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-365">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="374a0-366">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-366">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="374a0-367">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="374a0-367">In the preceding code:</span></span>

* <span data-ttu-id="374a0-368">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-368">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="374a0-369">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-369">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="374a0-370">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-370">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="374a0-371">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-371">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="374a0-372">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="374a0-372">Exception/error handling</span></span>
   * <span data-ttu-id="374a0-373">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-373">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="374a0-374">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-374">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="374a0-375">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-375">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="374a0-376">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="374a0-376">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="374a0-377">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-377">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="374a0-378">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-378">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="374a0-379">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-379">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="374a0-380">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-380">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="374a0-381">쿠키 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-381">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="374a0-382">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-382">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="374a0-383">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-383">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="374a0-384">앱이 세션 상태를 사용하는 경우에는 쿠키 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-384">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="374a0-385">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-385">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="374a0-386">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-386">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="374a0-387"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-387"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="374a0-388">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-388">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="374a0-389">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-389">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="374a0-390">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="374a0-390">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="374a0-391">*wwwroot*에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-391">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="374a0-392">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-392">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="374a0-393">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-393">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="374a0-394">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-394">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="374a0-395">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-395">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="374a0-396">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-396">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="374a0-397">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-397">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="374a0-398"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-398">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="374a0-399">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="374a0-399">Use, Run, and Map</span></span>

<span data-ttu-id="374a0-400"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-400">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="374a0-401">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="374a0-401">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="374a0-402">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-402">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="374a0-403"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-403"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="374a0-404">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-404">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="374a0-405">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-405">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="374a0-406">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-406">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="374a0-407">요청</span><span class="sxs-lookup"><span data-stu-id="374a0-407">Request</span></span>             | <span data-ttu-id="374a0-408">응답</span><span class="sxs-lookup"><span data-stu-id="374a0-408">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="374a0-409">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="374a0-409">localhost:1234</span></span>      | <span data-ttu-id="374a0-410">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-410">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="374a0-411">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="374a0-411">localhost:1234/map1</span></span> | <span data-ttu-id="374a0-412">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="374a0-412">Map Test 1</span></span>                   |
| <span data-ttu-id="374a0-413">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="374a0-413">localhost:1234/map2</span></span> | <span data-ttu-id="374a0-414">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="374a0-414">Map Test 2</span></span>                   |
| <span data-ttu-id="374a0-415">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="374a0-415">localhost:1234/map3</span></span> | <span data-ttu-id="374a0-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-416">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="374a0-417">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-417">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="374a0-418"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-418"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="374a0-419">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-419">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="374a0-420">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-420">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="374a0-421">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-421">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="374a0-422">요청</span><span class="sxs-lookup"><span data-stu-id="374a0-422">Request</span></span>                       | <span data-ttu-id="374a0-423">응답</span><span class="sxs-lookup"><span data-stu-id="374a0-423">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="374a0-424">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="374a0-424">localhost:1234</span></span>                | <span data-ttu-id="374a0-425">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="374a0-425">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="374a0-426">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="374a0-426">localhost:1234/?branch=master</span></span> | <span data-ttu-id="374a0-427">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="374a0-427">Branch used = master</span></span>         |

<span data-ttu-id="374a0-428">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-428">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="374a0-429">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-429">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="374a0-430">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="374a0-430">Built-in middleware</span></span>

<span data-ttu-id="374a0-431">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-431">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="374a0-432">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-432">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="374a0-433">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-433">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="374a0-434">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="374a0-434">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="374a0-435">미들웨어</span><span class="sxs-lookup"><span data-stu-id="374a0-435">Middleware</span></span> | <span data-ttu-id="374a0-436">설명</span><span class="sxs-lookup"><span data-stu-id="374a0-436">Description</span></span> | <span data-ttu-id="374a0-437">순서</span><span class="sxs-lookup"><span data-stu-id="374a0-437">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="374a0-438">인증</span><span class="sxs-lookup"><span data-stu-id="374a0-438">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="374a0-439">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-439">Provides authentication support.</span></span> | <span data-ttu-id="374a0-440">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-440">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="374a0-441">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="374a0-441">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="374a0-442">쿠키 정책</span><span class="sxs-lookup"><span data-stu-id="374a0-442">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="374a0-443">개인 정보 저장과 관련한 사용자의 동의를 추적하고 쿠키 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-443">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="374a0-444">쿠키를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-444">Before middleware that issues cookies.</span></span> <span data-ttu-id="374a0-445">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="374a0-445">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="374a0-446">CORS</span><span class="sxs-lookup"><span data-stu-id="374a0-446">CORS</span></span>](xref:security/cors) | <span data-ttu-id="374a0-447">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-447">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="374a0-448">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-448">Before components that use CORS.</span></span> |
| [<span data-ttu-id="374a0-449">진단</span><span class="sxs-lookup"><span data-stu-id="374a0-449">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="374a0-450">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="374a0-450">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="374a0-451">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-451">Before components that generate errors.</span></span> <span data-ttu-id="374a0-452">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-452">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="374a0-453">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="374a0-453">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="374a0-454">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-454">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="374a0-455">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="374a0-455">Before components that consume the updated fields.</span></span> <span data-ttu-id="374a0-456">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="374a0-456">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="374a0-457">상태 검사</span><span class="sxs-lookup"><span data-stu-id="374a0-457">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="374a0-458">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-458">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="374a0-459">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-459">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="374a0-460">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="374a0-460">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="374a0-461">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-461">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="374a0-462">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-462">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="374a0-463">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="374a0-463">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="374a0-464">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-464">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="374a0-465">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-465">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="374a0-466">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="374a0-466">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="374a0-467">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-467">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="374a0-468">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="374a0-468">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="374a0-469">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="374a0-469">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="374a0-470">MVC</span><span class="sxs-lookup"><span data-stu-id="374a0-470">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="374a0-471">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-471">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="374a0-472">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-472">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="374a0-473">OWIN</span><span class="sxs-lookup"><span data-stu-id="374a0-473">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="374a0-474">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-474">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="374a0-475">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-475">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="374a0-476">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="374a0-476">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="374a0-477">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-477">Provides support for caching responses.</span></span> | <span data-ttu-id="374a0-478">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-478">Before components that require caching.</span></span> |
| [<span data-ttu-id="374a0-479">응답 압축</span><span class="sxs-lookup"><span data-stu-id="374a0-479">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="374a0-480">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-480">Provides support for compressing responses.</span></span> | <span data-ttu-id="374a0-481">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-481">Before components that require compression.</span></span> |
| [<span data-ttu-id="374a0-482">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="374a0-482">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="374a0-483">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-483">Provides localization support.</span></span> | <span data-ttu-id="374a0-484">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-484">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="374a0-485">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="374a0-485">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="374a0-486">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-486">Defines and constrains request routes.</span></span> | <span data-ttu-id="374a0-487">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="374a0-487">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="374a0-488">세션</span><span class="sxs-lookup"><span data-stu-id="374a0-488">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="374a0-489">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-489">Provides support for managing user sessions.</span></span> | <span data-ttu-id="374a0-490">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-490">Before components that require Session.</span></span> |
| [<span data-ttu-id="374a0-491">정적 파일</span><span class="sxs-lookup"><span data-stu-id="374a0-491">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="374a0-492">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-492">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="374a0-493">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-493">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="374a0-494">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="374a0-494">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="374a0-495">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-495">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="374a0-496">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-496">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="374a0-497">WebSockets</span><span class="sxs-lookup"><span data-stu-id="374a0-497">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="374a0-498">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="374a0-498">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="374a0-499">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="374a0-499">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="374a0-500">추가 자료</span><span class="sxs-lookup"><span data-stu-id="374a0-500">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
