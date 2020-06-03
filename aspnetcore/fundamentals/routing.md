---
<span data-ttu-id="57132-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-102">'Blazor'</span></span>
- <span data-ttu-id="57132-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-103">'Identity'</span></span>
- <span data-ttu-id="57132-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-105">'Razor'</span></span>
- <span data-ttu-id="57132-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-106">'SignalR' uid:</span></span> 

---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="57132-107">ASP.NET Core에서 라우팅</span><span class="sxs-lookup"><span data-stu-id="57132-107">Routing in ASP.NET Core</span></span>

<span data-ttu-id="57132-108">작성자: [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57132-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57132-109">라우팅은 들어오는 HTTP 요청을 일치시켜 앱의 실행 가능 엔드포인트로 디스패치하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-109">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span> <span data-ttu-id="57132-110">[엔드포인트](#endpoint)는 앱의 실행 가능 요청 처리 코드 단위입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-110">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span> <span data-ttu-id="57132-111">엔드포인트는 앱에서 정의되고 앱 시작 시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-111">Endpoints are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="57132-112">엔드포인트 일치 프로세스는 요청의 URL에서 값을 추출하고 요청 처리를 위해 이 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-112">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span> <span data-ttu-id="57132-113">또한 라우팅은 앱의 엔드포인트 정보를 사용하여 엔드포인트에 매핑되는 URL을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-113">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="57132-114">앱은 다음을 사용하여 라우팅을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-114">Apps can configure routing using:</span></span>

- <span data-ttu-id="57132-115">Controllers</span><span class="sxs-lookup"><span data-stu-id="57132-115">Controllers</span></span>
- Razor<span data-ttu-id="57132-116"> Pages</span><span class="sxs-lookup"><span data-stu-id="57132-116"> Pages</span></span>
- SignalR
- <span data-ttu-id="57132-117">gRPC 서비스</span><span class="sxs-lookup"><span data-stu-id="57132-117">gRPC Services</span></span>
- <span data-ttu-id="57132-118">[상태 검사](xref:host-and-deploy/health-checks)와 같은 엔드포인트 지원 [미들웨어](xref:fundamentals/middleware/index)</span><span class="sxs-lookup"><span data-stu-id="57132-118">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
- <span data-ttu-id="57132-119">라우팅에 등록된 대리자 및 람다</span><span class="sxs-lookup"><span data-stu-id="57132-119">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="57132-120">이 문서에서는 ASP.NET Core 라우팅의 하위 수준 세부 정보를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-120">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="57132-121">라우팅을 구성하는 방법은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-121">For information on configuring routing:</span></span>

* <span data-ttu-id="57132-122">컨트롤러의 경우 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-122">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="57132-123">Razor Pages 규칙은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-123">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="57132-124">이 문서에서 설명하는 엔드포인트 라우팅 시스템은 ASP.NET Core 3.0 이상에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-124">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="57132-125"><xref:Microsoft.AspNetCore.Routing.IRouter> 기반의 이전 라우팅 시스템에 대한 자세한 내용은 다음 접근 방법 중 하나를 사용하여 ASP.NET Core 2.1 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-125">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span>

* <span data-ttu-id="57132-126">이전 버전의 버전 선택기</span><span class="sxs-lookup"><span data-stu-id="57132-126">The version selector for a previous version.</span></span>
* <span data-ttu-id="57132-127">[ASP.NET Core 2.1 라우팅](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1) 선택</span><span class="sxs-lookup"><span data-stu-id="57132-127">Select [ASP.NET Core 2.1 routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="57132-128">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57132-128">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="57132-129">이 문서의 다운로드 샘플은 특정 `Startup` 클래스를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-129">The download samples for this document are enabled by a specific `Startup` class.</span></span> <span data-ttu-id="57132-130">특정 샘플을 실행하려면 *Program.cs*를 수정하여 원하는 `Startup` 클래스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-130">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="57132-131">라우팅 기본 사항</span><span class="sxs-lookup"><span data-stu-id="57132-131">Routing basics</span></span>

<span data-ttu-id="57132-132">모든 ASP.NET Core 템플릿에는 생성된 코드에서의 라우팅이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-132">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="57132-133">라우팅은 `Startup.Configure`의 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-133">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>

<span data-ttu-id="57132-134">다음 코드에서는 라우팅의 기본 예제를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-134">The following code shows a basic example of routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="57132-135">라우팅은 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 및 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>를 통해 등록된 미들웨어 쌍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-135">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

* <span data-ttu-id="57132-136">`UseRouting`은 경로 일치를 미들웨어 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-136">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="57132-137">이 미들웨어는 앱에 정의된 엔드포인트 집합을 확인하고 요청을 기반으로 [가장 일치하는 항목](#urlm)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-137">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
* <span data-ttu-id="57132-138">`UseEndpoints`는 엔드포인트 실행을 미들웨어 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-138">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span> <span data-ttu-id="57132-139">선택한 엔드포인트와 연결된 대리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-139">It runs the delegate associated with the selected endpoint.</span></span>

<span data-ttu-id="57132-140">앞의 예제에는 [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) 메서드를 사용하는 단일 ‘라우팅 대상 코드’ 엔드포인트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-140">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>

* <span data-ttu-id="57132-141">HTTP `GET` 요청이 루트 URL `/`로 전송되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-141">When an HTTP `GET` request is sent to the root URL `/`:</span></span>
  * <span data-ttu-id="57132-142">표시된 요청 대리자가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-142">The request delegate shown executes.</span></span>
  * <span data-ttu-id="57132-143">`Hello World!`가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-143">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="57132-144">기본적으로 루트 URL `/`는 `https://localhost:5001/`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-144">By default, the root URL `/` is `https://localhost:5001/`.</span></span>
* <span data-ttu-id="57132-145">요청 메서드가 `GET`이 아니거나 루트 URL이 `/`가 아니면 일치하는 경로가 없고 HTTP 404가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-145">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>

### <a name="endpoint"></a><span data-ttu-id="57132-146">엔드포인트</span><span class="sxs-lookup"><span data-stu-id="57132-146">Endpoint</span></span>

<a name="endpoint"></a>

<span data-ttu-id="57132-147">`MapGet` 메서드는 **엔드포인트**을 정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-147">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="57132-148">엔드포인트는 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-148">An endpoint is something that can be:</span></span>

* <span data-ttu-id="57132-149">URL 및 HTTP 메서드를 일치시켜 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-149">Selected, by matching the URL and HTTP method.</span></span>
* <span data-ttu-id="57132-150">대리자를 실행하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-150">Executed, by running the delegate.</span></span>

<span data-ttu-id="57132-151">앱에서 일치시키고 실행할 수 있는 엔드포인트는 `UseEndpoints`에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-151">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span> <span data-ttu-id="57132-152">예를 들어 <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 및 [이들과 유사한 메서드](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions)는 요청 대리자를 라우팅 시스템에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-152">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span>
<span data-ttu-id="57132-153">ASP.NET Core Framework 기능을 라우팅 시스템에 연결하는 데 다음과 같은 추가 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-153">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>
- <span data-ttu-id="57132-154">[Razor Pages의 MapRazorPages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="57132-154">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>
- [<span data-ttu-id="57132-155">컨트롤러의 MapControllers</span><span class="sxs-lookup"><span data-stu-id="57132-155">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- <span data-ttu-id="57132-156">[SignalR의 MapHub\<THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="57132-156">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> 
- [<span data-ttu-id="57132-157">gRPC의 MapGrpcService\<TService></span><span class="sxs-lookup"><span data-stu-id="57132-157">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

<span data-ttu-id="57132-158">다음 예제에서는 더 복잡한 경로 템플릿을 사용한 라우팅을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-158">The following example shows routing with a more sophisticated route template:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

<span data-ttu-id="57132-159">`/hello/{name:alpha}` 문자열은 **경로 템플릿**이며</span><span class="sxs-lookup"><span data-stu-id="57132-159">The string `/hello/{name:alpha}` is a **route template**.</span></span> <span data-ttu-id="57132-160">엔드포인트가 일치되는 방식을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-160">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="57132-161">이 경우 템플릿은 다음과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-161">In this case, the template matches:</span></span>

* <span data-ttu-id="57132-162">`/hello/Ryan`과 같은 URL</span><span class="sxs-lookup"><span data-stu-id="57132-162">A URL like `/hello/Ryan`</span></span>
* <span data-ttu-id="57132-163">`/hello/`로 시작하고 그 다음에 시퀀스 영문자가 오는 URL 경로.</span><span class="sxs-lookup"><span data-stu-id="57132-163">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>  <span data-ttu-id="57132-164">`:alpha`는 영문자와만 일치하는 경로 제약 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-164">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span> <span data-ttu-id="57132-165">[경로 제약 조건](#route-constraint-reference)은 이 문서의 뒷부분에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-165">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span>

<span data-ttu-id="57132-166">URL 경로의 두 번째 세그먼트 `{name:alpha}`는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-166">The second segment of the URL path, `{name:alpha}`:</span></span>

* <span data-ttu-id="57132-167">`name` 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-167">Is bound to the `name` parameter.</span></span>
* <span data-ttu-id="57132-168">[HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)에 캡처되고 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-168">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

<span data-ttu-id="57132-169">이 문서에서 설명하는 엔드포인트는 라우팅 시스템은 ASP.NET Core 3.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-169">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="57132-170">그러나 모든 버전의 ASP.NET Core에서 동일한 경로 템플릿 기능 및 경로 제약 조건 집합을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-170">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="57132-171">다음 예제에서는 [상태 검사](xref:host-and-deploy/health-checks) 및 권한 부여를 사용한 라우팅을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-171">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="57132-172">앞의 예제에서는 다음을 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-172">The preceding example demonstrates how:</span></span>

* <span data-ttu-id="57132-173">권한 부여 미들웨어를 라우팅에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-173">The authorization middleware can be used with routing.</span></span>
* <span data-ttu-id="57132-174">엔드포인트를 사용하여 권한 부여 동작을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-174">Endpoints can be used to configure authorization behavior.</span></span>

<span data-ttu-id="57132-175"><xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> 호출은 상태 검사 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-175">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span> <span data-ttu-id="57132-176">이 호출에 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>을 연결하면 권한 부여 정책이 엔드포인트에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-176">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<span data-ttu-id="57132-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*>을 호출하면 인증 및 권한 부여 미들웨어가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-177">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span> <span data-ttu-id="57132-178">이러한 미들웨어는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*>과 `UseEndpoints` 사이에 배치되므로 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-178">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span>

* <span data-ttu-id="57132-179">`UseRouting`에서 선택된 엔드포인트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-179">See which endpoint was selected by `UseRouting`.</span></span>
* <span data-ttu-id="57132-180">엔드포인트로 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>가 디스패치되기 전에 권한 부여 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-180">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="57132-181">엔드포인트 메타데이터</span><span class="sxs-lookup"><span data-stu-id="57132-181">Endpoint metadata</span></span>

<span data-ttu-id="57132-182">앞의 예제에는 두 개의 엔드포인트가 있지만 상태 검사 엔드포인트에만 권한 부여 정책이 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-182">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span> <span data-ttu-id="57132-183">요청이 상태 검사 엔드포인트 `/healthz`와 일치하는 경우 권한 부여 확인이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-183">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span> <span data-ttu-id="57132-184">이는 엔드포인트에 추가 데이터가 연결될 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-184">This demonstrates that endpoints can have extra data attached to them.</span></span> <span data-ttu-id="57132-185">이러한 추가 데이터를 엔드포인트 **메타데이터**라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-185">This extra data is called endpoint **metadata**:</span></span>

* <span data-ttu-id="57132-186">이 메타데이터는 라우팅 인식 미들웨어에서 처리될 수 있으며</span><span class="sxs-lookup"><span data-stu-id="57132-186">The metadata can be processed by routing-aware middleware.</span></span>
* <span data-ttu-id="57132-187">모든 .NET 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-187">The metadata can be of any .NET type.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="57132-188">라우팅 개념</span><span class="sxs-lookup"><span data-stu-id="57132-188">Routing concepts</span></span>

<span data-ttu-id="57132-189">라우팅 시스템은 강력한 **엔드포인트** 개념을 추가하여 미들웨어 파이프라인을 기반으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-189">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span> <span data-ttu-id="57132-190">엔드포인트는 라우팅, 권한 부여 및 ASP.NET Core 시스템 수의 측면에서 서로 다른 앱의 기능 단위를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57132-190">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="57132-191">ASP.NET Core 엔드포인트 정의</span><span class="sxs-lookup"><span data-stu-id="57132-191">ASP.NET Core endpoint definition</span></span>

<span data-ttu-id="57132-192">ASP.NET Core 엔드포인트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-192">An ASP.NET Core endpoint is:</span></span>

* <span data-ttu-id="57132-193">실행 가능: <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-193">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>
* <span data-ttu-id="57132-194">확장 가능: [메타데이터](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) 컬렉션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-194">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span>
* <span data-ttu-id="57132-195">Selectable: 필요에 따라 [라우팅 정보](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-195">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span>
* <span data-ttu-id="57132-196">열거 가능: [DI](xref:fundamentals/dependency-injection)에서 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>를 검색하여 엔드포인트 컬렉션을 나열할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-196">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="57132-197">다음 코드에서는 현재 요청과 일치하는 엔드포인트를 검색하고 검사하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-197">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="57132-198">엔드포인트는 선택된 경우 `HttpContext`에서 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-198">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span> <span data-ttu-id="57132-199">해당 속성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-199">Its properties can be inspected.</span></span> <span data-ttu-id="57132-200">엔드포인트 개체는 변경할 수 없으며 만든 후 수정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-200">Endpoint objects are immutable and cannot be modified after creation.</span></span> <span data-ttu-id="57132-201">가장 일반적인 형식의 엔드포인트는 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-201">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="57132-202">`RouteEndpoint`에는 라우팅 시스템에서 선택할 수 있는 정보가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-202">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="57132-203">앞의 코드에서 [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*)는 인라인 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-203">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

<a name="mt"></a>

<span data-ttu-id="57132-204">다음 코드에서는 파이프라인에서 `app.Use`가 호출되는 위치에 따라 엔드포인트가 없을 수 있음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-204">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="57132-205">위의 샘플에서는 엔드포인트가 선택되었는지 아닌지를 표시하는 `Console.WriteLine` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-205">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span> <span data-ttu-id="57132-206">명확하게 하도록 이 샘플에서는 제공된 `/` 엔드포인트에 표시 이름을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-206">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>

<span data-ttu-id="57132-207">`/`의 URL을 사용하여 이 코드를 실행하면 다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-207">Running this code with a URL of `/` displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="57132-208">다른 URL을 사용하여 이 코드를 실행하면 다음이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-208">Running this code with any other URL displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="57132-209">이 출력은 다음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57132-209">This output demonstrates that:</span></span>

* <span data-ttu-id="57132-210">`UseRouting`을 호출하기 전에 엔드포인트는 항상 null입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-210">The endpoint is always null before `UseRouting` is called.</span></span>
* <span data-ttu-id="57132-211">일치 항목이 발견되면 `UseRouting`와 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>사이에서 엔드포인트가 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-211">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="57132-212">일치 항목이 발견되면 `UseEndpoints` 미들웨어가 **터미널**입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-212">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="57132-213">[터미널 미들웨어](#tm)는 이 문서의 뒷부분에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-213">[Terminal middleware](#tm) is defined later in this document.</span></span>
* <span data-ttu-id="57132-214">`UseEndpoints` 뒤의 미들웨어는 일치 항목이 없는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-214">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="57132-215">`UseRouting` 미들웨어는 [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) 메서드를 사용하여 엔드포인트를 현재 컨텍스트에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-215">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="57132-216">`UseRouting` 미들웨어를 사용자 지정 논리로 바꾸어도 엔드포인트를 사용하는 이점을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-216">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="57132-217">엔드포인트는 미들웨어와 같은 하위 수준 기본 형식이며 라우팅 구현에 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-217">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span> <span data-ttu-id="57132-218">대부분의 앱에서는 `UseRouting`을 사용자 지정 논리로 바꿀 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-218">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

<span data-ttu-id="57132-219">`UseEndpoints` 미들웨어는 `UseRouting` 미들웨어와 함께 사용하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-219">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span> <span data-ttu-id="57132-220">엔드포인트를 실행하는 핵심 논리는 복잡하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-220">The core logic to execute an endpoint isn't complicated.</span></span> <span data-ttu-id="57132-221"><xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>를 사용하여 엔드포인트를 검색한 다음 해당 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 속성을 호출하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-221">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>

<span data-ttu-id="57132-222">다음 코드에서는 미들웨어가 라우팅에 영향을 주거나 반응하는 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-222">The following code demonstrates how middleware can influence or react to routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="57132-223">앞의 예제에서는 다음과 같은 두 가지 중요한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-223">The preceding example demonstrates two important concepts:</span></span>

* <span data-ttu-id="57132-224">미들웨어는 `UseRouting` 전에 실행되어 라우팅 작동의 기반이 되는 데이터를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-224">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
    * <span data-ttu-id="57132-225">일반적으로 라우팅 전에 표시되는 미들웨어는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>또는 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>와 같은 요청의 일부 속성을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-225">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
* <span data-ttu-id="57132-226">미들웨어는 `UseRouting`과 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 사이에서 실행되어 엔드포인트가 실행되기 전에 라우팅의 결과를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-226">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>
    * <span data-ttu-id="57132-227">`UseRouting`과 `UseEndpoints` 사이에서 실행되는 미들웨어는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-227">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span>
      * <span data-ttu-id="57132-228">일반적으로 메타데이터를 검사하여 엔드포인트를 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-228">Usually inspects metadata to understand the endpoints.</span></span>
      * <span data-ttu-id="57132-229">`UseAuthorization` 및 `UseCors`에서 하는 것처럼 보안 결정을 내리는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-229">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="57132-230">미들웨어와 메타데이터를 조합하면 엔드포인트별로 정책을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-230">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span>

<span data-ttu-id="57132-231">위의 코드에서는 엔드포인트별 정책을 지원하는 사용자 지정 미들웨어의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-231">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="57132-232">이 미들웨어는 중요한 데이터에 대한 액세스의 ‘감사 로그’를 콘솔에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-232">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="57132-233">`AuditPolicyAttribute` 메타데이터를 사용하여 엔드포인트를 ‘감사’하도록 미들웨어를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-233">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span> <span data-ttu-id="57132-234">이 샘플에서는 중요함으로 표시된 엔드포인트만 감사되는 ‘옵트인 패턴’을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-234">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="57132-235">예를 들어 이 논리를 역으로 정의하여 안전한 것으로 표시되지 않은 모든 항목을 감사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-235">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="57132-236">엔드포인트 메타데이터 시스템은 유연합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-236">The endpoint metadata system is flexible.</span></span> <span data-ttu-id="57132-237">이 논리는 사용 사례에 적합한 방식으로 설계할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-237">This logic could be designed in whatever way suits the use case.</span></span>

<span data-ttu-id="57132-238">앞의 샘플 코드는 엔드포인트의 기본 개념을 보여 주기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-238">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span> <span data-ttu-id="57132-239">**프로덕션 용도로는 사용하지 않아야 합니다**.</span><span class="sxs-lookup"><span data-stu-id="57132-239">**The sample is not intended for production use**.</span></span> <span data-ttu-id="57132-240">‘감사 로그’ 미들웨어의 전체 버전은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-240">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="57132-241">파일이나 데이터베이스에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-241">Log to a file or database.</span></span>
* <span data-ttu-id="57132-242">사용자, IP 주소, 중요한 엔드포인트의 이름 등과 같은 세부 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-242">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="57132-243">감사 정책 메타데이터 `AuditPolicyAttribute`는 컨트롤러 및 SignalR 같은 클래스 기반 프레임워크에서 더욱 쉽게 사용할 수 있도록 `Attribute`로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-243">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="57132-244">‘라우팅 대상 코드’를 사용하면 다음과 같이.</span><span class="sxs-lookup"><span data-stu-id="57132-244">When using *route to code*:</span></span>

* <span data-ttu-id="57132-245">메타데이터가 작성기 API와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-245">Metadata is attached with a builder API.</span></span>
* <span data-ttu-id="57132-246">엔드포인트를 만들 때 해당 메서드 및 클래스의 모든 특성이 클래스 기반 프레임워크에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-246">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="57132-247">메타데이터 형식은 인터페이스나 특성으로 정의하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-247">The best practices for metadata types are to define them either as interfaces or attributes.</span></span> <span data-ttu-id="57132-248">인터페이스 및 특성을 사용하면 코드를 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-248">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="57132-249">메타데이터 시스템은 유연하며 제한을 적용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-249">The metadata system is flexible and doesn't impose any limitations.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="57132-250">터미널 미들웨어와 라우팅 비교</span><span class="sxs-lookup"><span data-stu-id="57132-250">Comparing a terminal middleware and routing</span></span>

<span data-ttu-id="57132-251">다음 코드 샘플에서는 미들웨어 사용과 라우팅 사용을 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-251">The following code sample contrasts using middleware with using routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="57132-252">`Approach 1:`로 표시된 미들웨어 스타일은 **터미널 미들웨어**입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-252">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span> <span data-ttu-id="57132-253">일치 작업을 수행하기 때문에 터미널 미들웨어라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-253">It's called terminal middleware because it does a matching operation:</span></span>

* <span data-ttu-id="57132-254">위 샘플의 일치 작업은 미들웨어의 `Path == "/"`와 라우팅의 `Path == "/Movie"`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-254">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
* <span data-ttu-id="57132-255">일치가 성공하면 일부 기능을 실행하고 `next` 미들웨어를 호출하는 대신 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-255">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>

<span data-ttu-id="57132-256">검색을 종료하고 일부 기능을 실행한 다음 반환하기 때문에 터미널 미들웨어라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-256">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>

<span data-ttu-id="57132-257">터미널 미들웨어와 라우팅을 비교하면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-257">Comparing a terminal middleware and routing:</span></span>
* <span data-ttu-id="57132-258">두 방법 모두 처리 파이프라인을 종료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-258">Both approaches allow terminating the processing pipeline:</span></span>
    * <span data-ttu-id="57132-259">미들웨어는 `next`를 호출하는 대신 반환하여 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-259">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="57132-260">엔드포인트는 항상 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-260">Endpoints are always terminal.</span></span>
* <span data-ttu-id="57132-261">터미널 미들웨어를 사용하면 파이프라인에서 미들웨어를 임의로 위치 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-261">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span>
    * <span data-ttu-id="57132-262">엔드포인트는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>의 위치에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-262">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="57132-263">터미널 미들웨어는 임의의 코드를 사용하여 미들웨어가 일치되는 시기를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-263">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="57132-264">사용자 지정 경로 일치 코드는 길어져서 올바르게 작성하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-264">Custom route matching code can be verbose and difficult to write correctly.</span></span>
    * <span data-ttu-id="57132-265">라우팅은 일반적인 앱을 위한 간단한 솔루션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-265">Routing provides straightforward solutions for typical apps.</span></span> <span data-ttu-id="57132-266">앱 대부분에는 사용자 지정 경로 일치 코드가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-266">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="57132-267">엔드포인트는 `UseAuthorization` 및 `UseCors` 같은 미들웨어와 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-267">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="57132-268">`UseAuthorization` 또는 `UseCors`와 함께 터미널 미들웨어를 사용하려면 권한 부여 시스템을 수동으로 조작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-268">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="57132-269">[엔드포인트](#endpoint)는 다음 두 가지를 모두 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-269">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="57132-270">요청을 처리할 대리자</span><span class="sxs-lookup"><span data-stu-id="57132-270">A delegate to process requests.</span></span>
* <span data-ttu-id="57132-271">임의 메타데이터의 컬렉션.</span><span class="sxs-lookup"><span data-stu-id="57132-271">A collection of arbitrary metadata.</span></span> <span data-ttu-id="57132-272">메타데이터는 각 엔드포인트에 연결된 정책과 구성에 따라 횡단 관심사(Cross-Cutting Concerns)를 구현하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-272">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="57132-273">터미널 미들웨어는 효과적인 도구이지만 다음이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-273">Terminal middleware can be an effective tool, but can require:</span></span>

* <span data-ttu-id="57132-274">상당한 양의 코딩과 테스트</span><span class="sxs-lookup"><span data-stu-id="57132-274">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="57132-275">원하는 수준의 유연성을 얻기 위한 다른 시스템과의 수동 통합</span><span class="sxs-lookup"><span data-stu-id="57132-275">Manual integration with other systems to achieve the desired level of flexibility.</span></span>

<span data-ttu-id="57132-276">터미널 미들웨어를 작성하기 전에 라우팅과 통합하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-276">Consider integrating with routing before writing a terminal middleware.</span></span>

<span data-ttu-id="57132-277">[또는](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>과 통합되는 기존 터미널 미들웨어는 일반적으로 라우팅 인식 엔드포인트로 전환될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-277">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="57132-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16)는 다음과 같은 라우터 방식의 패턴을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="57132-279"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>에 대한 확장 메서드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-279">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="57132-280"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>를 사용하여 중첩된 미들웨어 파이프라인을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-280">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="57132-281">새 파이프라인에 미들웨어를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-281">Attach the middleware to the new pipeline.</span></span> <span data-ttu-id="57132-282">이 예제의 경우 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-282">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>
* <span data-ttu-id="57132-283">미들웨어 파이프라인을 <xref:Microsoft.AspNetCore.Http.RequestDelegate>로 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-283"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="57132-284">`Map`을 호출하고 새 미들웨어 파이프라인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-284">Call `Map` and provide the new middleware pipeline.</span></span>
* <span data-ttu-id="57132-285">확장 메서드의 `Map`에서 제공하는 작성기 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-285">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="57132-286">다음 코드에서는 [MapHealthChecks](xref:host-and-deploy/health-checks)를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-286">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="57132-287">앞의 샘플에서는 작성기 개체를 반환하는 것이 중요한 이유를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-287">The preceding sample shows why returning the builder object is important.</span></span> <span data-ttu-id="57132-288">작성기 개체를 반환하면 앱 개발자가 엔드포인트의 권한 부여와 같은 정책을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-288">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span> <span data-ttu-id="57132-289">이 예제에서는 상태 검사 미들웨어가 권한 부여 시스템과 직접 통합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-289">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>

<span data-ttu-id="57132-290">메타데이터 시스템은 터미널 미들웨어를 사용하는 확장성 작성자에서 발생한 문제에 대응하여 만들어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-290">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span> <span data-ttu-id="57132-291">미들웨어마다 권한 부여 시스템과의 고유한 통합을 구현하는 것은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-291">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="57132-292">URL 일치</span><span class="sxs-lookup"><span data-stu-id="57132-292">URL matching</span></span>

* <span data-ttu-id="57132-293">라우팅이 들어오는 요청을 [엔드포인트](#endpoint)와 일치시키는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-293">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="57132-294">URL 경로 및 헤더의 데이터를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-294">Is based on data in the URL path and headers.</span></span>
* <span data-ttu-id="57132-295">요청의 모든 데이터를 고려하도록 확장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-295">Can be extended to consider any data in the request.</span></span>

<span data-ttu-id="57132-296">라우팅 미들웨어가 실행되면 `Endpoint`를 설정하여 현재 요청에서 <xref:Microsoft.AspNetCore.Http.HttpContext>의 [요청 기능](xref:fundamentals/request-features)으로 값을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-296">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span>

* <span data-ttu-id="57132-297">[HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>)를 호출하면 엔드포인트를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-297">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>
* <span data-ttu-id="57132-298">`HttpRequest.RouteValues`는 경로 값의 컬렉션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-298">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="57132-299">라우팅 미들웨어 뒤에 실행되는 [미들웨어](xref:fundamentals/middleware/index)는 엔드포인트를 검사하고 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-299">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span> <span data-ttu-id="57132-300">예를 들어 권한 부여 미들웨어는 엔드포인트의 메타데이터 컬렉션에서 권한 부여 정책을 조사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-300">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="57132-301">요청 처리 파이프라인의 미들웨어가 모두 실행된 후에 선택한 엔드포인트의 대리자가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-301">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="57132-302">엔드포인트 라우팅의 라우팅 시스템은 모든 디스패치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-302">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="57132-303">미들웨어는 선택된 엔드포인트에 기반으로 하여 정책을 적용하므로 다음이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-303">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>

* <span data-ttu-id="57132-304">디스패치나 보안 정책의 애플리케이션에 영향을 줄 수 있는 모든 결정은 라우팅 시스템 내에서 내려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-304">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

> [!WARNING]
> <span data-ttu-id="57132-305">이전 버전과의 호환성을 위해 컨트롤러 또는 Razor Pages 엔드포인트 대리자가 실행되면 [RouteContext RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성이 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-305">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>
>
> <span data-ttu-id="57132-306">`RouteContext` 형식은 이후 릴리스에서 obsolete로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-306">The `RouteContext` type will be marked obsolete in a future release:</span></span>
>
> * <span data-ttu-id="57132-307">`RouteData.Values`를 `HttpRequest.RouteValues`로 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-307">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span>
> * <span data-ttu-id="57132-308">`RouteData.DataTokens`를 마이그레이션하여 엔드포인트 메타데이터에서 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata)를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-308">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span>

<span data-ttu-id="57132-309">URL 일치는 구성 가능한 일련의 단계로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-309">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="57132-310">각 단계의 출력은 일치 항목 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-310">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="57132-311">일치 항목 집합은 다음 단계에서 더욱 좁혀질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-311">The set of matches can be narrowed down further by the next phase.</span></span> <span data-ttu-id="57132-312">라우팅 구현에서는 일치하는 엔드포인트의 처리 순서를 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-312">The routing implementation does not guarantee a processing order for matching endpoints.</span></span> <span data-ttu-id="57132-313">기능한 **모든** 일치 항목이 한 번에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-313">**All** possible matches are processed at once.</span></span> <span data-ttu-id="57132-314">URL 일치 단계는 다음 순서로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-314">The URL matching phases occur in the following order.</span></span> <span data-ttu-id="57132-315">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="57132-315">ASP.NET Core:</span></span>

1. <span data-ttu-id="57132-316">엔드포인트와 해당 경로 템플릿 집합에 대한 URL 경로를 처리하여 일치 항목을 **모두** 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-316">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>
1. <span data-ttu-id="57132-317">앞의 목록을 사용하여 경로 제약 조건이 적용되지 않는 일치 항목을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-317">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
1. <span data-ttu-id="57132-318">앞의 목록을 사용하여 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 인스턴스 집합에서 실패하는 항목을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-318">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>
1. <span data-ttu-id="57132-319">[EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector)를 사용하여 앞의 목록을 통해 최종 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="57132-319">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span>

<span data-ttu-id="57132-320">엔드포인트 목록의 우선 순위는 다음에 따라 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-320">The list of endpoints is prioritized according to:</span></span>

* <span data-ttu-id="57132-321">[RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span><span class="sxs-lookup"><span data-stu-id="57132-321">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span>
* <span data-ttu-id="57132-322">[경로 템플릿 우선 순위](#rtp)</span><span class="sxs-lookup"><span data-stu-id="57132-322">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="57132-323"><xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector>에 도달할 때까지 각 단계에서 일치하는 모든 엔드포인트가 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-323">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="57132-324">`EndpointSelector`는 최종 단계이며,</span><span class="sxs-lookup"><span data-stu-id="57132-324">The `EndpointSelector` is the final phase.</span></span> <span data-ttu-id="57132-325">일치 항목에서 가장 높은 우선 순위 엔드포인트를 가장 일치하는 항목으로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-325">It chooses the highest priority endpoint from the matches as the best match.</span></span> <span data-ttu-id="57132-326">가장 일치하는 항목과 같은 우선 순위의 다른 일치 항목이 있으면 모호한 일치 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-326">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="57132-327">경로 우선 순위는 **더 구체적인** 경로 템플릿에 높은 우선 순위가 지정되는 기준에 따라 컴퓨팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-327">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="57132-328">예를 들어 `/hello` 및 `/{message}` 템플릿을 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-328">For example, consider the templates `/hello` and `/{message}`:</span></span>

* <span data-ttu-id="57132-329">둘 다 URL 경로 `/hello`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-329">Both match the URL path `/hello`.</span></span>
* <span data-ttu-id="57132-330">`/hello`가 더 구체적이므로 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-330">`/hello`  is more specific and therefore higher priority.</span></span>

<span data-ttu-id="57132-331">일반적으로 경로 우선 순위는 실제로 사용되는 URL 체계에 가장 일치하는 항목을 선택하는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-331">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span> <span data-ttu-id="57132-332">모호성을 방지하는 데 필요한 경우에만 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-332">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>

<span data-ttu-id="57132-333">라우팅에서 제공하는 확장성의 종류 때문에 라우팅 시스템이 모호한 경로를 미리 컴퓨팅할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-333">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span> <span data-ttu-id="57132-334">경로 템플릿 `/{message:alpha}` 및 `/{message:int}`와 같은 예제를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-334">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

* <span data-ttu-id="57132-335">`alpha` 제약 조건은 영문자와만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-335">The `alpha` constraint matches only alphabetic characters.</span></span>
* <span data-ttu-id="57132-336">`int` 제약 조건은 숫자와만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-336">The `int` constraint matches only numbers.</span></span>
* <span data-ttu-id="57132-337">이러한 템플릿은 경로 우선 순위가 동일하지만 둘 다와 일치하는 단일 URL은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-337">These templates have the same route precedence, but there's no single URL they both match.</span></span>
* <span data-ttu-id="57132-338">라우팅 시스템에서 시작 시 모호성 오류를 보고한 경우 이 유효한 사용 사례를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-338">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span>

> [!WARNING]
>
> <span data-ttu-id="57132-339"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 내의 작업 순서는 라우팅 동작에 영향을 주지 않지만 한 가지 예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-339">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span> <span data-ttu-id="57132-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>는 호출된 순서를 기준으로 해당 엔드포인트에 순서 값을 자동으로 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="57132-341">이는 이전 라우팅 구현과 동일한 보장을 제공하는 라우팅 시스템이 없이 컨트롤러의 오래된 동작을 시뮬레이트합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-341">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span>
>
> <span data-ttu-id="57132-342">라우팅의 레거시 구현에서는 경로가 처리되는 순서에 종속된 라우팅 확장성을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-342">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span> <span data-ttu-id="57132-343">ASP.NET Core 3.0 이상의 엔드포인트 라우팅은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-343">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>
> 
> * <span data-ttu-id="57132-344">경로 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-344">Doesn't have a concept of routes.</span></span>
> * <span data-ttu-id="57132-345">순서 지정을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-345">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="57132-346">모든 엔드포인트가 한 번에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-346">All endpoints are processed at once.</span></span>
>
> <span data-ttu-id="57132-347">이 때문에 레거시 라우팅 시스템을 계속 사용하는 경우 [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues)(지원을 위한 GitHub 문제를 시작)하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-347">If this means you're stuck using the legacy routing system, [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues).</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="57132-348">경로 템플릿 우선 순위 및 엔드포인트 선택 영역 순서</span><span class="sxs-lookup"><span data-stu-id="57132-348">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="57132-349">[경로 템플릿 우선 순위](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16)는 얼마나 구체적인지를 기준으로 각 경로 템플릿에 값을 할당하는 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-349">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="57132-350">경로 템플릿 우선 순위의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-350">Route template precedence:</span></span>

* <span data-ttu-id="57132-351">일반적인 사례에서 엔드포인트 순서를 조정할 필요가 없게 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-351">Avoids the need to adjust the order of endpoints in common cases.</span></span>
* <span data-ttu-id="57132-352">라우팅 동작에 관한 일반적인 기대에 맞추려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-352">Attempts to match the common-sense expectations of routing behavior.</span></span>

<span data-ttu-id="57132-353">예를 들어 `/Products/List` 및 `/Products/{id}` 템플릿을 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-353">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span> <span data-ttu-id="57132-354">URL 경로 `/Products/List`에 대해 `/Products/List`가 `/Products/{id}`보다 더 잘 일치한다고 합리적으로 가정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-354">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span> <span data-ttu-id="57132-355">이유는 리터럴 세그먼트 `/List`가 매개 변수 세그먼트 `/{id}`보다 더 우선 순위가 높다고 간주되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-355">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>

<span data-ttu-id="57132-356">우선 순위의 작동 방식에 대한 세부 정보는 경로 템플릿이 정의된 방법과 어느 정도 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-356">The details of how precedence works are coupled to how route templates are defined:</span></span>

* <span data-ttu-id="57132-357">세그먼트가 더 많은 템플릿은 더 구체적인 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-357">Templates with more segments are considered more specific.</span></span>
* <span data-ttu-id="57132-358">리터럴 텍스트가 있는 세그먼트가 매개 변수 세그먼트보다 더 구체적인 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-358">A segment with literal text is considered more specific than a parameter segment.</span></span>
* <span data-ttu-id="57132-359">제약 조건이 있는 매개 변수 세그먼트가 제약 조건이 없는 매개 변수 세그먼트보다 더 구체적인 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-359">A parameter segment with a constraint is considered more specific than one without.</span></span>
* <span data-ttu-id="57132-360">복잡한 세그먼트는 제약 조건이 있는 매개 변수 세그먼트만큼 구체적인 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-360">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>
* <span data-ttu-id="57132-361">Catch-all 매개 변수가 가장 덜 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-361">Catch-all parameters are the least specific.</span></span> <span data-ttu-id="57132-362">Catch-all 경로에 관한 중요한 내용은 [경로 템플릿 참조](#rtr)에서 **catch-all**을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-362">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>

<span data-ttu-id="57132-363">정확한 값 참조는 [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189)(GitHub의 소스 코드)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-363">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="57132-364">URL 생성 개념</span><span class="sxs-lookup"><span data-stu-id="57132-364">URL generation concepts</span></span>

<span data-ttu-id="57132-365">URL 생성은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-365">URL generation:</span></span>

* <span data-ttu-id="57132-366">라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-366">Is the process by which routing can create a URL path based on a set of route values.</span></span>
* <span data-ttu-id="57132-367">엔드포인트와 이에 액세스하는 URL을 논리적으로 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-367">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="57132-368">엔드포인트 라우팅에는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-368">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="57132-369">`LinkGenerator`는 [DI](xref:fundamentals/dependency-injection)에서 사용할 수 있는 싱글톤 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-369">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57132-370">`LinkGenerator` API는 실행 중인 요청의 컨텍스트 외부에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-370">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="57132-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) 및 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 사용하는 시나리오(예: [태그 도우미](xref:mvc/views/tag-helpers/intro), HTML 도우미 및 [작업 결과](xref:mvc/controllers/actions))는 내부적으로 `LinkGenerator` API를 사용하여 링크 생성 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

<span data-ttu-id="57132-372">링크 생성기는 **주소** 및 **주소 체계**의 개념으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-372">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="57132-373">주소 체계는 링크 생성을 위해 고려해야 할 엔드포인트를 결정하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-373">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="57132-374">예를 들어 컨트롤러 및 Razor Pages에서 많은 사용자에게 친숙한 경로 이름 및 경로 값 시나리오는 주소 체계로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-374">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="57132-375">링크 생성기는 다음 확장 메서드를 통해 컨트롤러 및 Razor Pages에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-375">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="57132-376">이러한 메서드의 오버로드에는 `HttpContext`를 포함한 인수가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-376">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="57132-377">이러한 메서드는 기능적으로 [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) 및 [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)와 동일하지만, 추가적인 유연성과 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-377">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="57132-378">`GetPath*` 메서드는 절대 경로가 포함된 URI를 생성한다는 점에서 `Url.Action` 및 `Url.Page`와 가장 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-378">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="57132-379">`GetUri*` 메서드는 항상 체계와 호스트를 포함한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-379">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="57132-380">`HttpContext`를 허용하는 메서드는 실행 중인 요청의 컨텍스트에서 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-380">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="57132-381">재정의되지 않는 한 실행 중인 요청의 [앰비언트](#ambient) 경로 값, URL 기본 경로, 체계 및 호스트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-381">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="57132-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 주소를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="57132-383">URI 생성은 다음 두 단계로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-383">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="57132-384">주소는 해당 주소와 일치하는 엔드포인트 목록에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-384">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="57132-385">제공된 값과 일치하는 경로 패턴을 찾을 때까지 각 엔드포인트의 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>이 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-385">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="57132-386">결과 출력은 링크 생성기에 제공된 다른 URI 부분과 결합되어 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-386">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="57132-387"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>에서 제공하는 메서드는 모든 유형의 주소에 대해 표준 링크 생성 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-387">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="57132-388">링크 생성기를 사용하는 가장 편리한 방법은 특정 주소 유형에 대한 작업을 수행하는 확장 메서드를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-388">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>

| <span data-ttu-id="57132-389">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="57132-389">Extension Method</span></span> | <span data-ttu-id="57132-390">설명</span><span class="sxs-lookup"><span data-stu-id="57132-390">Description</span></span> |
| ---
<span data-ttu-id="57132-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-392">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-392">'Blazor'</span></span>
- <span data-ttu-id="57132-393">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-393">'Identity'</span></span>
- <span data-ttu-id="57132-394">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-394">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-395">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-395">'Razor'</span></span>
- <span data-ttu-id="57132-396">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-396">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-398">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-398">'Blazor'</span></span>
- <span data-ttu-id="57132-399">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-399">'Identity'</span></span>
- <span data-ttu-id="57132-400">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-400">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-401">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-401">'Razor'</span></span>
- <span data-ttu-id="57132-402">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-402">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-404">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-404">'Blazor'</span></span>
- <span data-ttu-id="57132-405">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-405">'Identity'</span></span>
- <span data-ttu-id="57132-406">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-406">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-407">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-407">'Razor'</span></span>
- <span data-ttu-id="57132-408">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-408">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-410">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-410">'Blazor'</span></span>
- <span data-ttu-id="57132-411">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-411">'Identity'</span></span>
- <span data-ttu-id="57132-412">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-412">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-413">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-413">'Razor'</span></span>
- <span data-ttu-id="57132-414">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-414">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-416">'Blazor'</span></span>
- <span data-ttu-id="57132-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-417">'Identity'</span></span>
- <span data-ttu-id="57132-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-419">'Razor'</span></span>
- <span data-ttu-id="57132-420">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-422">'Blazor'</span></span>
- <span data-ttu-id="57132-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-423">'Identity'</span></span>
- <span data-ttu-id="57132-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-425">'Razor'</span></span>
- <span data-ttu-id="57132-426">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-426">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-428">'Blazor'</span></span>
- <span data-ttu-id="57132-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-429">'Identity'</span></span>
- <span data-ttu-id="57132-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-431">'Razor'</span></span>
- <span data-ttu-id="57132-432">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-434">'Blazor'</span></span>
- <span data-ttu-id="57132-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-435">'Identity'</span></span>
- <span data-ttu-id="57132-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-437">'Razor'</span></span>
- <span data-ttu-id="57132-438">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-440">'Blazor'</span></span>
- <span data-ttu-id="57132-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-441">'Identity'</span></span>
- <span data-ttu-id="57132-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-443">'Razor'</span></span>
- <span data-ttu-id="57132-444">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-444">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 제공된 값에 기반한 절대 경로의 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="57132-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 제공된 값에 기반한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="57132-447"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 메서드 호출 시 다음과 같은 의미에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-447">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="57132-448">들어오는 요청의 `GetUri*` 헤더의 유효성을 검사하지 않는 앱 구성에서는 `Host` 확장 메서드를 신중하게 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-448">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="57132-449">들어오는 요청의 `Host` 헤더의 유효성을 검사하지 않으면 신뢰할 수 없는 요청 입력이 보기 또는 페이지에 포함된 URI로 클라이언트에 다시 보내질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-449">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span> <span data-ttu-id="57132-450">모든 프로덕션 앱은 알려진 유효한 값에 대해 `Host` 헤더의 유효성을 검사하도록 서버를 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-450">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="57132-451">미들웨어에서 `MapWhen` 또는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>과 함께 `Map`를 사용할 때는 신중하게 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-451">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="57132-452">`Map*`는 실행 중인 요청의 기본 경로를 변경하여 링크 생성의 출력에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-452">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="57132-453">모든 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API는 기본 경로를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-453">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="57132-454">링크 생성에 대한 `Map*`의 영향을 실행 취소하려면 빈 기본 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-454">Specify an empty base path to undo the `Map*` affect on link generation.</span></span>

### <a name="middleware-example"></a><span data-ttu-id="57132-455">미들웨어 예제</span><span class="sxs-lookup"><span data-stu-id="57132-455">Middleware example</span></span>

<span data-ttu-id="57132-456">다음 예제에서는 미들웨어에서 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 상점 제품을 나열하는 작업 메서드에 대한 링크를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-456">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span> <span data-ttu-id="57132-457">링크 생성기를 클래스에 주입하고 `GenerateLink`를 호출하여 앱의 모든 클래스에서 해당 링크 생성기를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-457">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="57132-458">경로 템플릿 참조</span><span class="sxs-lookup"><span data-stu-id="57132-458">Route template reference</span></span>

<span data-ttu-id="57132-459">`{}` 내의 토큰은 경로가 일치하는 경우 바인딩될 경로 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-459">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="57132-460">경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 경로 매개 변수를 리터럴 값으로 분리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-460">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span> <span data-ttu-id="57132-461">예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-461">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>  <span data-ttu-id="57132-462">경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-462">Route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="57132-463">경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-463">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="57132-464">텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-464">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="57132-465">리터럴 경로 매개 변수 구분 기호 `{` 또는 `}`와 일치시키려면 문자를</span><span class="sxs-lookup"><span data-stu-id="57132-465">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="57132-466">반복하여(예: `{{` 또는 `}}`) 구분 기호를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-466">For example `{{` or `}}`.</span></span>

<span data-ttu-id="57132-467">별표 `*` 또는 이중 별표 `**`:</span><span class="sxs-lookup"><span data-stu-id="57132-467">Asterisk `*` or double asterisk `**`:</span></span>

* <span data-ttu-id="57132-468">URI의 나머지 부분에 바인딩하기 위해 경로 매개 변수의 접두사로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-468">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>
* <span data-ttu-id="57132-469">**범용** 매개 변수라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-469">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="57132-470">예: `blog/{**slug}`</span><span class="sxs-lookup"><span data-stu-id="57132-470">For example, `blog/{**slug}`:</span></span>
  * <span data-ttu-id="57132-471">`/blog`로 시작하고 그 다음에 임의의 값이 오는 모든 URI를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-471">Matches any URI that starts with `/blog` and has any value following it.</span></span>
  * <span data-ttu-id="57132-472">`/blog` 다음의 값은 [동적 필드](https://developer.mozilla.org/docs/Glossary/Slug) 경로 값에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-472">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="57132-473">범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-473">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="57132-474">범용 매개 변수는 경로 구분 기호 `/` 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-474">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span> <span data-ttu-id="57132-475">예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-475">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="57132-476">이스케이프된 슬래시에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-476">Note the escaped forward slash.</span></span> <span data-ttu-id="57132-477">경로 구분 기호 문자를 왕복하려면 `**` 경로 매개 변수 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-477">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="57132-478">`{ path = "my/path" }`를 사용하는 경로 `foo/{**path}`는 `foo/my/path`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-478">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="57132-479">선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-479">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="57132-480">예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-480">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="57132-481">`filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="57132-481">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="57132-482">URL에 `filename`에 대한 값만 있으면 후행 `.`가 선택 사항이므로 경로가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-482">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="57132-483">다음 URL은 이 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-483">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="57132-484">경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 **기본값**이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-484">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="57132-485">예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-485">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="57132-486">URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-486">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="57132-487">경로 매개 변수는 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-487">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span> <span data-ttu-id="57132-488">예: `id?`.</span><span class="sxs-lookup"><span data-stu-id="57132-488">For example, `id?`.</span></span> <span data-ttu-id="57132-489">선택적 값과 기본 경로 매개 변수의 차이는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-489">The difference between optional values and default route parameters is:</span></span>

* <span data-ttu-id="57132-490">기본값이 있는 경로 매개 변수는 항상 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-490">A route parameter with a default value always produces a value.</span></span>
* <span data-ttu-id="57132-491">선택적 매개 변수는 요청 URL에서 값을 제공한 경우에만 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-491">An optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="57132-492">경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-492">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="57132-493">경로 매개 변수 이름 뒤에 `:`과 제약 조건 이름을 추가하여 경로 매개 변수에서 인라인 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-493">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="57132-494">제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에서 괄호 `(...)`로 묶입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-494">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="57132-495">또 다른 `:` 및 제약 조건 이름을 추가하여 여러 ‘인라인 제약 조건’을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-495">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span>

<span data-ttu-id="57132-496">제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-496">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="57132-497">예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-497">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="57132-498">경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-498">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="57132-499">경로 매개 변수에는 매개 변수 변환기가 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-499">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="57132-500">매개 변수 변환기는 링크를 생성하고 URL에 대한 작업 및 페이지와 일치할 때 매개 변수 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-500">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="57132-501">제약 조건과 마찬가지로, 매개 변수 변환기는 경로 매개 변수 이름 뒤에 `:`과 변환기 이름을 추가하여 경로 매개 변수에 인라인으로 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-501">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span> <span data-ttu-id="57132-502">예를 들어 경로 템플릿 `blog/{article:slugify}`는 `slugify` 변환기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-502">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="57132-503">매개 변수 변환기에 대한 자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-503">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="57132-504">다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-504">The following table demonstrates example route templates and their behavior:</span></span>

| <span data-ttu-id="57132-505">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="57132-505">Route Template</span></span>                           | <span data-ttu-id="57132-506">URI 일치 예제</span><span class="sxs-lookup"><span data-stu-id="57132-506">Example Matching URI</span></span>    | <span data-ttu-id="57132-507">요청 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="57132-507">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="57132-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-509">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-509">'Blazor'</span></span>
- <span data-ttu-id="57132-510">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-510">'Identity'</span></span>
- <span data-ttu-id="57132-511">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-511">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-512">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-512">'Razor'</span></span>
- <span data-ttu-id="57132-513">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-513">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-515">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-515">'Blazor'</span></span>
- <span data-ttu-id="57132-516">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-516">'Identity'</span></span>
- <span data-ttu-id="57132-517">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-517">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-518">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-518">'Razor'</span></span>
- <span data-ttu-id="57132-519">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-519">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-521">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-521">'Blazor'</span></span>
- <span data-ttu-id="57132-522">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-522">'Identity'</span></span>
- <span data-ttu-id="57132-523">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-523">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-524">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-524">'Razor'</span></span>
- <span data-ttu-id="57132-525">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-525">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-527">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-527">'Blazor'</span></span>
- <span data-ttu-id="57132-528">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-528">'Identity'</span></span>
- <span data-ttu-id="57132-529">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-529">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-530">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-530">'Razor'</span></span>
- <span data-ttu-id="57132-531">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-531">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-533">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-533">'Blazor'</span></span>
- <span data-ttu-id="57132-534">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-534">'Identity'</span></span>
- <span data-ttu-id="57132-535">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-535">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-536">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-536">'Razor'</span></span>
- <span data-ttu-id="57132-537">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-537">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-539">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-539">'Blazor'</span></span>
- <span data-ttu-id="57132-540">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-540">'Identity'</span></span>
- <span data-ttu-id="57132-541">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-541">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-542">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-542">'Razor'</span></span>
- <span data-ttu-id="57132-543">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-543">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-545">'Blazor'</span></span>
- <span data-ttu-id="57132-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-546">'Identity'</span></span>
- <span data-ttu-id="57132-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-548">'Razor'</span></span>
- <span data-ttu-id="57132-549">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-551">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-551">'Blazor'</span></span>
- <span data-ttu-id="57132-552">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-552">'Identity'</span></span>
- <span data-ttu-id="57132-553">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-553">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-554">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-554">'Razor'</span></span>
- <span data-ttu-id="57132-555">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-555">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-557">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-557">'Blazor'</span></span>
- <span data-ttu-id="57132-558">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-558">'Identity'</span></span>
- <span data-ttu-id="57132-559">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-559">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-560">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-560">'Razor'</span></span>
- <span data-ttu-id="57132-561">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-561">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-563">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-563">'Blazor'</span></span>
- <span data-ttu-id="57132-564">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-564">'Identity'</span></span>
- <span data-ttu-id="57132-565">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-565">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-566">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-566">'Razor'</span></span>
- <span data-ttu-id="57132-567">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-567">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-569">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-569">'Blazor'</span></span>
- <span data-ttu-id="57132-570">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-570">'Identity'</span></span>
- <span data-ttu-id="57132-571">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-571">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-572">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-572">'Razor'</span></span>
- <span data-ttu-id="57132-573">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-573">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-575">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-575">'Blazor'</span></span>
- <span data-ttu-id="57132-576">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-576">'Identity'</span></span>
- <span data-ttu-id="57132-577">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-577">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-578">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-578">'Razor'</span></span>
- <span data-ttu-id="57132-579">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-579">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-581">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-581">'Blazor'</span></span>
- <span data-ttu-id="57132-582">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-582">'Identity'</span></span>
- <span data-ttu-id="57132-583">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-583">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-584">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-584">'Razor'</span></span>
- <span data-ttu-id="57132-585">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-585">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-587">'Blazor'</span></span>
- <span data-ttu-id="57132-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-588">'Identity'</span></span>
- <span data-ttu-id="57132-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-590">'Razor'</span></span>
- <span data-ttu-id="57132-591">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-593">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-593">'Blazor'</span></span>
- <span data-ttu-id="57132-594">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-594">'Identity'</span></span>
- <span data-ttu-id="57132-595">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-595">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-596">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-596">'Razor'</span></span>
- <span data-ttu-id="57132-597">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-597">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-599">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-599">'Blazor'</span></span>
- <span data-ttu-id="57132-600">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-600">'Identity'</span></span>
- <span data-ttu-id="57132-601">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-601">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-602">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-602">'Razor'</span></span>
- <span data-ttu-id="57132-603">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-603">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-605">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-605">'Blazor'</span></span>
- <span data-ttu-id="57132-606">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-606">'Identity'</span></span>
- <span data-ttu-id="57132-607">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-607">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-608">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-608">'Razor'</span></span>
- <span data-ttu-id="57132-609">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-609">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-611">'Blazor'</span></span>
- <span data-ttu-id="57132-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-612">'Identity'</span></span>
- <span data-ttu-id="57132-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-614">'Razor'</span></span>
- <span data-ttu-id="57132-615">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-615">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-617">'Blazor'</span></span>
- <span data-ttu-id="57132-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-618">'Identity'</span></span>
- <span data-ttu-id="57132-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-620">'Razor'</span></span>
- <span data-ttu-id="57132-621">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-623">'Blazor'</span></span>
- <span data-ttu-id="57132-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-624">'Identity'</span></span>
- <span data-ttu-id="57132-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-626">'Razor'</span></span>
- <span data-ttu-id="57132-627">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-629">'Blazor'</span></span>
- <span data-ttu-id="57132-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-630">'Identity'</span></span>
- <span data-ttu-id="57132-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-632">'Razor'</span></span>
- <span data-ttu-id="57132-633">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-635">'Blazor'</span></span>
- <span data-ttu-id="57132-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-636">'Identity'</span></span>
- <span data-ttu-id="57132-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-638">'Razor'</span></span>
- <span data-ttu-id="57132-639">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-641">'Blazor'</span></span>
- <span data-ttu-id="57132-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-642">'Identity'</span></span>
- <span data-ttu-id="57132-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-644">'Razor'</span></span>
- <span data-ttu-id="57132-645">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-647">'Blazor'</span></span>
- <span data-ttu-id="57132-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-648">'Identity'</span></span>
- <span data-ttu-id="57132-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-650">'Razor'</span></span>
- <span data-ttu-id="57132-651">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-653">'Blazor'</span></span>
- <span data-ttu-id="57132-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-654">'Identity'</span></span>
- <span data-ttu-id="57132-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-656">'Razor'</span></span>
- <span data-ttu-id="57132-657">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-657">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-659">'Blazor'</span></span>
- <span data-ttu-id="57132-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-660">'Identity'</span></span>
- <span data-ttu-id="57132-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-662">'Razor'</span></span>
- <span data-ttu-id="57132-663">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-663">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-665">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-665">'Blazor'</span></span>
- <span data-ttu-id="57132-666">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-666">'Identity'</span></span>
- <span data-ttu-id="57132-667">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-667">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-668">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-668">'Razor'</span></span>
- <span data-ttu-id="57132-669">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-669">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-671">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-671">'Blazor'</span></span>
- <span data-ttu-id="57132-672">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-672">'Identity'</span></span>
- <span data-ttu-id="57132-673">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-673">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-674">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-674">'Razor'</span></span>
- <span data-ttu-id="57132-675">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-675">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-677">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-677">'Blazor'</span></span>
- <span data-ttu-id="57132-678">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-678">'Identity'</span></span>
- <span data-ttu-id="57132-679">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-679">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-680">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-680">'Razor'</span></span>
- <span data-ttu-id="57132-681">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-681">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-683">'Blazor'</span></span>
- <span data-ttu-id="57132-684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-684">'Identity'</span></span>
- <span data-ttu-id="57132-685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-685">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-686">'Razor'</span></span>
- <span data-ttu-id="57132-687">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-689">'Blazor'</span></span>
- <span data-ttu-id="57132-690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-690">'Identity'</span></span>
- <span data-ttu-id="57132-691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-691">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-692">'Razor'</span></span>
- <span data-ttu-id="57132-693">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-695">'Blazor'</span></span>
- <span data-ttu-id="57132-696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-696">'Identity'</span></span>
- <span data-ttu-id="57132-697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-697">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-698">'Razor'</span></span>
- <span data-ttu-id="57132-699">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-701">'Blazor'</span></span>
- <span data-ttu-id="57132-702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-702">'Identity'</span></span>
- <span data-ttu-id="57132-703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-703">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-704">'Razor'</span></span>
- <span data-ttu-id="57132-705">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-707">'Blazor'</span></span>
- <span data-ttu-id="57132-708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-708">'Identity'</span></span>
- <span data-ttu-id="57132-709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-709">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-710">'Razor'</span></span>
- <span data-ttu-id="57132-711">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-713">'Blazor'</span></span>
- <span data-ttu-id="57132-714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-714">'Identity'</span></span>
- <span data-ttu-id="57132-715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-715">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-716">'Razor'</span></span>
- <span data-ttu-id="57132-717">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-719">'Blazor'</span></span>
- <span data-ttu-id="57132-720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-720">'Identity'</span></span>
- <span data-ttu-id="57132-721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-721">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-722">'Razor'</span></span>
- <span data-ttu-id="57132-723">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-725">'Blazor'</span></span>
- <span data-ttu-id="57132-726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-726">'Identity'</span></span>
- <span data-ttu-id="57132-727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-727">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-728">'Razor'</span></span>
- <span data-ttu-id="57132-729">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-731">'Blazor'</span></span>
- <span data-ttu-id="57132-732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-732">'Identity'</span></span>
- <span data-ttu-id="57132-733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-733">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-734">'Razor'</span></span>
- <span data-ttu-id="57132-735">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-737">'Blazor'</span></span>
- <span data-ttu-id="57132-738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-738">'Identity'</span></span>
- <span data-ttu-id="57132-739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-739">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-740">'Razor'</span></span>
- <span data-ttu-id="57132-741">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-741">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-743">'Blazor'</span></span>
- <span data-ttu-id="57132-744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-744">'Identity'</span></span>
- <span data-ttu-id="57132-745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-745">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-746">'Razor'</span></span>
- <span data-ttu-id="57132-747">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-749">'Blazor'</span></span>
- <span data-ttu-id="57132-750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-750">'Identity'</span></span>
- <span data-ttu-id="57132-751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-751">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-752">'Razor'</span></span>
- <span data-ttu-id="57132-753">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-755">'Blazor'</span></span>
- <span data-ttu-id="57132-756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-756">'Identity'</span></span>
- <span data-ttu-id="57132-757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-757">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-758">'Razor'</span></span>
- <span data-ttu-id="57132-759">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-761">'Blazor'</span></span>
- <span data-ttu-id="57132-762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-762">'Identity'</span></span>
- <span data-ttu-id="57132-763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-763">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-764">'Razor'</span></span>
- <span data-ttu-id="57132-765">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-767">'Blazor'</span></span>
- <span data-ttu-id="57132-768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-768">'Identity'</span></span>
- <span data-ttu-id="57132-769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-769">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-770">'Razor'</span></span>
- <span data-ttu-id="57132-771">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-773">'Blazor'</span></span>
- <span data-ttu-id="57132-774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-774">'Identity'</span></span>
- <span data-ttu-id="57132-775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-775">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-776">'Razor'</span></span>
- <span data-ttu-id="57132-777">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-779">'Blazor'</span></span>
- <span data-ttu-id="57132-780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-780">'Identity'</span></span>
- <span data-ttu-id="57132-781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-781">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-782">'Razor'</span></span>
- <span data-ttu-id="57132-783">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-785">'Blazor'</span></span>
- <span data-ttu-id="57132-786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-786">'Identity'</span></span>
- <span data-ttu-id="57132-787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-787">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-788">'Razor'</span></span>
- <span data-ttu-id="57132-789">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-791">'Blazor'</span></span>
- <span data-ttu-id="57132-792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-792">'Identity'</span></span>
- <span data-ttu-id="57132-793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-793">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-794">'Razor'</span></span>
- <span data-ttu-id="57132-795">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-797">'Blazor'</span></span>
- <span data-ttu-id="57132-798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-798">'Identity'</span></span>
- <span data-ttu-id="57132-799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-799">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-800">'Razor'</span></span>
- <span data-ttu-id="57132-801">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-803">'Blazor'</span></span>
- <span data-ttu-id="57132-804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-804">'Identity'</span></span>
- <span data-ttu-id="57132-805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-805">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-806">'Razor'</span></span>
- <span data-ttu-id="57132-807">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-807">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-809">'Blazor'</span></span>
- <span data-ttu-id="57132-810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-810">'Identity'</span></span>
- <span data-ttu-id="57132-811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-811">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-812">'Razor'</span></span>
- <span data-ttu-id="57132-813">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-815">'Blazor'</span></span>
- <span data-ttu-id="57132-816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-816">'Identity'</span></span>
- <span data-ttu-id="57132-817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-817">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-818">'Razor'</span></span>
- <span data-ttu-id="57132-819">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-821">'Blazor'</span></span>
- <span data-ttu-id="57132-822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-822">'Identity'</span></span>
- <span data-ttu-id="57132-823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-823">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-824">'Razor'</span></span>
- <span data-ttu-id="57132-825">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-827">'Blazor'</span></span>
- <span data-ttu-id="57132-828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-828">'Identity'</span></span>
- <span data-ttu-id="57132-829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-829">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-830">'Razor'</span></span>
- <span data-ttu-id="57132-831">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-833">'Blazor'</span></span>
- <span data-ttu-id="57132-834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-834">'Identity'</span></span>
- <span data-ttu-id="57132-835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-835">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-836">'Razor'</span></span>
- <span data-ttu-id="57132-837">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-839">'Blazor'</span></span>
- <span data-ttu-id="57132-840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-840">'Identity'</span></span>
- <span data-ttu-id="57132-841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-841">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-842">'Razor'</span></span>
- <span data-ttu-id="57132-843">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-845">'Blazor'</span></span>
- <span data-ttu-id="57132-846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-846">'Identity'</span></span>
- <span data-ttu-id="57132-847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-847">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-848">'Razor'</span></span>
- <span data-ttu-id="57132-849">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-851">'Blazor'</span></span>
- <span data-ttu-id="57132-852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-852">'Identity'</span></span>
- <span data-ttu-id="57132-853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-853">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-854">'Razor'</span></span>
- <span data-ttu-id="57132-855">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-857">'Blazor'</span></span>
- <span data-ttu-id="57132-858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-858">'Identity'</span></span>
- <span data-ttu-id="57132-859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-859">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-860">'Razor'</span></span>
- <span data-ttu-id="57132-861">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-863">'Blazor'</span></span>
- <span data-ttu-id="57132-864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-864">'Identity'</span></span>
- <span data-ttu-id="57132-865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-865">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-866">'Razor'</span></span>
- <span data-ttu-id="57132-867">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-869">'Blazor'</span></span>
- <span data-ttu-id="57132-870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-870">'Identity'</span></span>
- <span data-ttu-id="57132-871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-871">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-872">'Razor'</span></span>
- <span data-ttu-id="57132-873">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-875">'Blazor'</span></span>
- <span data-ttu-id="57132-876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-876">'Identity'</span></span>
- <span data-ttu-id="57132-877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-877">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-878">'Razor'</span></span>
- <span data-ttu-id="57132-879">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-879">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-880">------------------------------------- | | `hello`                                  | `/hello`                | 단일 경로 `/hello`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-880">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="57132-881">| | `{Page=Home}`                            | `/`                     | `Page`를 찾아 `Home`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-881">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="57132-882">| | `{Page=Home}`                            | `/Contact`              | `Page`를 찾아 `Contact`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-882">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="57132-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="57132-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="57132-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> <span data-ttu-id="57132-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="57132-886">`id`는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-886">`id` is ignored.</span></span>        <span data-ttu-id="57132-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | `Products` 컨트롤러 및 `Index` 메서드에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Maps to the `Products` controller and `Index` method.</span></span> <span data-ttu-id="57132-888">`id`는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-888">`id` is ignored.</span></span>        |

<span data-ttu-id="57132-889">템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-889">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="57132-890">제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-890">Constraints and defaults can also be specified outside the route template.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="57132-891">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="57132-891">Complex segments</span></span>

<span data-ttu-id="57132-892">복잡한 세그먼트는 [non-greedy](#greedy) 방식으로 오른쪽에서 왼쪽으로 리터럴 구분 기호를 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-892">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span> <span data-ttu-id="57132-893">예를 들어 `[Route("/a{b}c{d}")]`는 복잡한 세그먼트입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-893">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span>
<span data-ttu-id="57132-894">복잡한 세그먼트는 특정 방식으로 작동하므로 제대로 사용하려면 이 방식을 이해해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-894">Complex segments work in a particular way that must be understood to use them successfully.</span></span> <span data-ttu-id="57132-895">이 단원의 예제에서는 매개 변수 내에 구분 기호 텍스트가 표시되지 않는 경우에만 복잡한 세그먼트가 실제로 잘 작동하는 이유를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-895">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="57132-896">더욱 복잡한 경우에는 [regex](/dotnet/standard/base-types/regular-expressions)를 사용한 다음 값을 수동으로 추출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-896">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="57132-897">다음은 라우팅에서 `/a{b}c{d}` 템플릿 및 URL 경로 `/abcd`를 사용하여 수행하는 단계를 요약한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-897">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span> <span data-ttu-id="57132-898">`|`는 알고리즘의 작동 방식을 시각화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-898">The `|` is used to help visualize how the algorithm works:</span></span>

* <span data-ttu-id="57132-899">오른쪽에서 왼쪽으로 첫 번째 리터럴은 `c`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-899">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="57132-900">따라서 `/abcd`가 오른쪽에서 검색되고 `/ab|c|d`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-900">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>
* <span data-ttu-id="57132-901">이제 오른쪽의 모든 항목(`d`)이 경로 매개 변수 `{d}`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-901">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="57132-902">오른쪽에서 왼쪽으로 다음 리터럴은 `a`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-902">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="57132-903">따라서 `/ab|c|d`가 중단된 위치부터 검색되고 `a`를 `/|a|b|c|d`와 함께 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-903">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>
* <span data-ttu-id="57132-904">이제 오른쪽 값(`b`)이 경로 매개 변수 `{b}`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-904">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="57132-905">남은 텍스트가 없고 남은 경로 템플릿도 없으므로 이것이 일치 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-905">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="57132-906">다음은 동일한 템플릿 `/a{b}c{d}`와 URL 경로 `/aabcd`를 사용하는 부정적인 사례의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-906">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span> <span data-ttu-id="57132-907">`|`는 알고리즘의 작동 방식을 시각화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-907">The `|` is used to help visualize how the algorithm works.</span></span> <span data-ttu-id="57132-908">이 경우는 동일한 알고리즘으로 설명되는 일치 항목이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-908">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="57132-909">오른쪽에서 왼쪽으로 첫 번째 리터럴은 `c`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-909">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="57132-910">따라서 `/aabcd`가 오른쪽에서 검색되고 `/aab|c|d`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-910">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="57132-911">이제 오른쪽의 모든 항목(`d`)이 경로 매개 변수 `{d}`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-911">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="57132-912">오른쪽에서 왼쪽으로 다음 리터럴은 `a`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-912">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="57132-913">따라서 `/aab|c|d`가 중단된 위치부터 검색되고 `a`를 `/a|a|b|c|d`와 함께 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-913">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="57132-914">이제 오른쪽 값(`b`)이 경로 매개 변수 `{b}`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-914">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="57132-915">이제 남은 텍스트 `a`가 있지만 알고리즘에서 구문 분석할 경로 템플릿이 부족하므로 이것은 일치 항목이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-915">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

<span data-ttu-id="57132-916">일치 알고리즘이 [non-greedy](#greedy)이므로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-916">Since the matching algorithm is [non-greedy](#greedy):</span></span>

* <span data-ttu-id="57132-917">각 단계에서 가능한 최소의 텍스트와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-917">It matches the smallest amount of text possible in each step.</span></span>
* <span data-ttu-id="57132-918">매개 변수 값 내에 구분 기호 값이 표시되는 모든 경우에는 일치하지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-918">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span>

<span data-ttu-id="57132-919">정규식을 사용하면 일치 동작을 더 효율적으로 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-919">Regular expressions provide much more control over their matching behavior.</span></span>

<a name="greedy"></a>

<span data-ttu-id="57132-920">[지연 일치](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)라고도 하는 greedy 일치는 가능한 가장 큰 문자열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-920">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="57132-921">Non-greedy는 가능한 가장 작은 문자열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-921">Non-greedy matches the smallest possible string.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="57132-922">경로 제약 조건 참조</span><span class="sxs-lookup"><span data-stu-id="57132-922">Route constraint reference</span></span>

<span data-ttu-id="57132-923">경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-923">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="57132-924">일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 true 또는 false 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="57132-924">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="57132-925">일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-925">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="57132-926">예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-926">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="57132-927">제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-927">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="57132-928">제약 조건을 입력 유효성 검사에 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="57132-928">Don't use constraints for input validation.</span></span> <span data-ttu-id="57132-929">입력 유효성 검사에 제약 조건을 사용하면 잘못된 입력으로 인해 `404` 찾을 수 없음 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-929">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span> <span data-ttu-id="57132-930">입력이 잘못되면 `400` 잘못된 요청과 해당하는 오류 메시지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-930">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="57132-931">경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 명확하게 구분하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-931">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="57132-932">다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-932">The following table demonstrates example route constraints and their expected behavior:</span></span>

| <span data-ttu-id="57132-933">제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-933">constraint</span></span> | <span data-ttu-id="57132-934">예제</span><span class="sxs-lookup"><span data-stu-id="57132-934">Example</span></span> | <span data-ttu-id="57132-935">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="57132-935">Example Matches</span></span> | <span data-ttu-id="57132-936">참고</span><span class="sxs-lookup"><span data-stu-id="57132-936">Notes</span></span> |
| ---
<span data-ttu-id="57132-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-938">'Blazor'</span></span>
- <span data-ttu-id="57132-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-939">'Identity'</span></span>
- <span data-ttu-id="57132-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-941">'Razor'</span></span>
- <span data-ttu-id="57132-942">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-944">'Blazor'</span></span>
- <span data-ttu-id="57132-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-945">'Identity'</span></span>
- <span data-ttu-id="57132-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-947">'Razor'</span></span>
- <span data-ttu-id="57132-948">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-950">'Blazor'</span></span>
- <span data-ttu-id="57132-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-951">'Identity'</span></span>
- <span data-ttu-id="57132-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-953">'Razor'</span></span>
- <span data-ttu-id="57132-954">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-954">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-956">'Blazor'</span></span>
- <span data-ttu-id="57132-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-957">'Identity'</span></span>
- <span data-ttu-id="57132-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-959">'Razor'</span></span>
- <span data-ttu-id="57132-960">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-960">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-962">'Blazor'</span></span>
- <span data-ttu-id="57132-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-963">'Identity'</span></span>
- <span data-ttu-id="57132-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-965">'Razor'</span></span>
- <span data-ttu-id="57132-966">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-968">'Blazor'</span></span>
- <span data-ttu-id="57132-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-969">'Identity'</span></span>
- <span data-ttu-id="57132-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-971">'Razor'</span></span>
- <span data-ttu-id="57132-972">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-974">'Blazor'</span></span>
- <span data-ttu-id="57132-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-975">'Identity'</span></span>
- <span data-ttu-id="57132-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-977">'Razor'</span></span>
- <span data-ttu-id="57132-978">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-980">'Blazor'</span></span>
- <span data-ttu-id="57132-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-981">'Identity'</span></span>
- <span data-ttu-id="57132-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-983">'Razor'</span></span>
- <span data-ttu-id="57132-984">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-986">'Blazor'</span></span>
- <span data-ttu-id="57132-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-987">'Identity'</span></span>
- <span data-ttu-id="57132-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-989">'Razor'</span></span>
- <span data-ttu-id="57132-990">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-990">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치합니다. | | `bool` | `{active:bool}` | `true`, `FALSE` | `true` 또는 `false`와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false`.</span></span> <span data-ttu-id="57132-992">대/소문자를 구분하지 않습니다. | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 고정 문화권에서 유효한 `DateTime` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-992">Case-insensitive | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="57132-993">위의 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-993">See preceding warning.</span></span> <span data-ttu-id="57132-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 고정 문화권에서 유효한 `decimal` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="57132-995">위의 경고를 참조하세요.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 고정 문화권에서 유효한 `double` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-995">See preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="57132-996">위의 경고를 참조하세요.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 고정 문화권에서 유효한 `float` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-996">See preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="57132-997">위의 경고를 참조하세요.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 유효한 `Guid` 값을 찾습니다. | | `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 `long` 값을 찾습니다. | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열은 4자 이상이어야 합니다. | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 문자열은 8자 이상이어야 합니다. | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열은 정확히 12자여야 합니다. | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열은 8자 이상이고 16자 이하여야 합니다. | | `min(value)` | `{age:min(18)}` | `19` | 정수 값은 18 이상이어야 합니다. | | `max(value)` | `{age:max(120)}` | `91` | 정수 값은 120 이상이어야 합니다. | | `range(min,max)` | `{age:range(18,120)}` | `91` | 정수 값은 18 이상이고 120 이하여야 합니다. | | `alpha` | `{name:alpha}` | `Rick` | 문자열은 하나 이상의 영문자(`a`-`z`, 대/소문자 구분 안 함)로 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-997">See preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> <span data-ttu-id="57132-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열은 정규식과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression.</span></span> <span data-ttu-id="57132-999">정규식을 정의하는 방법에 대한 팁을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-999">See tips about defining a regular expression.</span></span> <span data-ttu-id="57132-1000">| | `required` | `{name:required}` | `Rick` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됩니다. |</span><span class="sxs-lookup"><span data-stu-id="57132-1000">| | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="57132-1001">콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1001">Multiple, colon delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="57132-1002">예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1002">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="57132-1003">CLR 형식으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을</span><span class="sxs-lookup"><span data-stu-id="57132-1003">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span> <span data-ttu-id="57132-1004">사용합니다(예: CLR 형식 `int` 또는 `DateTime`으로 변환).</span><span class="sxs-lookup"><span data-stu-id="57132-1004">For example, conversion to the CLR type `int` or `DateTime`.</span></span> <span data-ttu-id="57132-1005">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1005">These constraints assume that the URL is not localizable.</span></span> <span data-ttu-id="57132-1006">프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1006">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="57132-1007">URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1007">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="57132-1008">예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1008">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="57132-1009">제약 조건의 정규식</span><span class="sxs-lookup"><span data-stu-id="57132-1009">Regular expressions in constraints</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="57132-1010">`regex(...)` 경로 제약 조건을 사용하여 정규식을 인라인 제약 조건으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1010">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span> <span data-ttu-id="57132-1011"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 제품군의 메서드에서는 개체 리터럴의 제약 조건도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1011">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span> <span data-ttu-id="57132-1012">해당 양식을 사용하는 경우 문자열 값이 정규식으로 해석됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1012">If that form is used, string values are interpreted as regular expressions.</span></span>

<span data-ttu-id="57132-1013">다음 코드에서는 인라인 regex 제약 조건을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1013">The following code uses an inline regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="57132-1014">다음 코드에서는 개체 리터럴을 사용하여 regex 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1014">The following code uses an object literal to specify a regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="57132-1015">ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1015">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="57132-1016">이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1016">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="57132-1017">정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1017">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="57132-1018">정규식 토큰은 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1018">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="57132-1019">인라인 제약 조건에서 정규식 `^\d{3}-\d{2}-\d{4}$`를 사용하려면 다음 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1019">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span>

* <span data-ttu-id="57132-1020">`\` 문자열 이스케이프 문자를 이스케이프하기 위해 C# 소스 파일에서 문자열에 제공된 `\` 문자를 `\\` 문자로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1020">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="57132-1021">[축자 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string).</span><span class="sxs-lookup"><span data-stu-id="57132-1021">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="57132-1022">라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(예: `{{`, `}}`, `[[`, `]]`).</span><span class="sxs-lookup"><span data-stu-id="57132-1022">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="57132-1023">다음 표에서는 정규식 및 이스케이프된 버전을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1023">The following table shows a regular expression and its escaped version:</span></span>

| <span data-ttu-id="57132-1024">정규식</span><span class="sxs-lookup"><span data-stu-id="57132-1024">Regular expression</span></span>    | <span data-ttu-id="57132-1025">이스케이프된 정규식</span><span class="sxs-lookup"><span data-stu-id="57132-1025">Escaped regular expression</span></span>     |
| ---
<span data-ttu-id="57132-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1027">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1027">'Blazor'</span></span>
- <span data-ttu-id="57132-1028">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1028">'Identity'</span></span>
- <span data-ttu-id="57132-1029">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1029">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1030">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1030">'Razor'</span></span>
- <span data-ttu-id="57132-1031">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1031">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1033">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1033">'Blazor'</span></span>
- <span data-ttu-id="57132-1034">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1034">'Identity'</span></span>
- <span data-ttu-id="57132-1035">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1035">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1036">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1036">'Razor'</span></span>
- <span data-ttu-id="57132-1037">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1037">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1039">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1039">'Blazor'</span></span>
- <span data-ttu-id="57132-1040">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1040">'Identity'</span></span>
- <span data-ttu-id="57132-1041">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1041">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1042">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1042">'Razor'</span></span>
- <span data-ttu-id="57132-1043">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1043">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1045">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1045">'Blazor'</span></span>
- <span data-ttu-id="57132-1046">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1046">'Identity'</span></span>
- <span data-ttu-id="57132-1047">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1047">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1048">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1048">'Razor'</span></span>
- <span data-ttu-id="57132-1049">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1049">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1051">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1051">'Blazor'</span></span>
- <span data-ttu-id="57132-1052">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1052">'Identity'</span></span>
- <span data-ttu-id="57132-1053">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1053">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1054">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1054">'Razor'</span></span>
- <span data-ttu-id="57132-1055">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1055">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1057">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1057">'Blazor'</span></span>
- <span data-ttu-id="57132-1058">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1058">'Identity'</span></span>
- <span data-ttu-id="57132-1059">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1059">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1060">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1060">'Razor'</span></span>
- <span data-ttu-id="57132-1061">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1061">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1063">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1063">'Blazor'</span></span>
- <span data-ttu-id="57132-1064">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1064">'Identity'</span></span>
- <span data-ttu-id="57132-1065">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1065">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1066">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1066">'Razor'</span></span>
- <span data-ttu-id="57132-1067">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1067">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1069">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1069">'Blazor'</span></span>
- <span data-ttu-id="57132-1070">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1070">'Identity'</span></span>
- <span data-ttu-id="57132-1071">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1071">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1072">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1072">'Razor'</span></span>
- <span data-ttu-id="57132-1073">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1073">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1075">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1075">'Blazor'</span></span>
- <span data-ttu-id="57132-1076">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1076">'Identity'</span></span>
- <span data-ttu-id="57132-1077">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1077">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1078">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1078">'Razor'</span></span>
- <span data-ttu-id="57132-1079">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1079">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1081">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1081">'Blazor'</span></span>
- <span data-ttu-id="57132-1082">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1082">'Identity'</span></span>
- <span data-ttu-id="57132-1083">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1083">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1084">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1084">'Razor'</span></span>
- <span data-ttu-id="57132-1085">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1085">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1087">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1087">'Blazor'</span></span>
- <span data-ttu-id="57132-1088">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1088">'Identity'</span></span>
- <span data-ttu-id="57132-1089">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1089">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1090">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1090">'Razor'</span></span>
- <span data-ttu-id="57132-1091">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1091">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1093">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1093">'Blazor'</span></span>
- <span data-ttu-id="57132-1094">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1094">'Identity'</span></span>
- <span data-ttu-id="57132-1095">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1095">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1096">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1096">'Razor'</span></span>
- <span data-ttu-id="57132-1097">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1097">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1099">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1099">'Blazor'</span></span>
- <span data-ttu-id="57132-1100">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1100">'Identity'</span></span>
- <span data-ttu-id="57132-1101">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1101">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1102">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1102">'Razor'</span></span>
- <span data-ttu-id="57132-1103">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1103">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1105">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1105">'Blazor'</span></span>
- <span data-ttu-id="57132-1106">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1106">'Identity'</span></span>
- <span data-ttu-id="57132-1107">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1107">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1108">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1108">'Razor'</span></span>
- <span data-ttu-id="57132-1109">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1109">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1111">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1111">'Blazor'</span></span>
- <span data-ttu-id="57132-1112">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1112">'Identity'</span></span>
- <span data-ttu-id="57132-1113">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1113">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1114">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1114">'Razor'</span></span>
- <span data-ttu-id="57132-1115">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1115">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1117">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1117">'Blazor'</span></span>
- <span data-ttu-id="57132-1118">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1118">'Identity'</span></span>
- <span data-ttu-id="57132-1119">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1119">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1120">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1120">'Razor'</span></span>
- <span data-ttu-id="57132-1121">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1121">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1123">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1123">'Blazor'</span></span>
- <span data-ttu-id="57132-1124">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1124">'Identity'</span></span>
- <span data-ttu-id="57132-1125">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1125">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1126">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1126">'Razor'</span></span>
- <span data-ttu-id="57132-1127">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1127">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1129">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1129">'Blazor'</span></span>
- <span data-ttu-id="57132-1130">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1130">'Identity'</span></span>
- <span data-ttu-id="57132-1131">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1131">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1132">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1132">'Razor'</span></span>
- <span data-ttu-id="57132-1133">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1133">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1135">'Blazor'</span></span>
- <span data-ttu-id="57132-1136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1136">'Identity'</span></span>
- <span data-ttu-id="57132-1137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1137">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1138">'Razor'</span></span>
- <span data-ttu-id="57132-1139">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1141">'Blazor'</span></span>
- <span data-ttu-id="57132-1142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1142">'Identity'</span></span>
- <span data-ttu-id="57132-1143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1143">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1144">'Razor'</span></span>
- <span data-ttu-id="57132-1145">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1147">'Blazor'</span></span>
- <span data-ttu-id="57132-1148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1148">'Identity'</span></span>
- <span data-ttu-id="57132-1149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1149">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1150">'Razor'</span></span>
- <span data-ttu-id="57132-1151">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1151">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="57132-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="57132-1153">라우팅에 사용되는 정규식은 `^` 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1153">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="57132-1154">식은 `$` 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1154">The expressions often end with the `$` character and match the end of the string.</span></span> <span data-ttu-id="57132-1155">`^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1155">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="57132-1156">`^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하지만, 이는 종종 원하는 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1156">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="57132-1157">다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1157">The following table provides examples and explains why they match or fail to match:</span></span>

| <span data-ttu-id="57132-1158">식</span><span class="sxs-lookup"><span data-stu-id="57132-1158">Expression</span></span>   | <span data-ttu-id="57132-1159">String</span><span class="sxs-lookup"><span data-stu-id="57132-1159">String</span></span>    | <span data-ttu-id="57132-1160">일치</span><span class="sxs-lookup"><span data-stu-id="57132-1160">Match</span></span> | <span data-ttu-id="57132-1161">주석</span><span class="sxs-lookup"><span data-stu-id="57132-1161">Comment</span></span>               |
| ---
<span data-ttu-id="57132-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1163">'Blazor'</span></span>
- <span data-ttu-id="57132-1164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1164">'Identity'</span></span>
- <span data-ttu-id="57132-1165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1165">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1166">'Razor'</span></span>
- <span data-ttu-id="57132-1167">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1169">'Blazor'</span></span>
- <span data-ttu-id="57132-1170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1170">'Identity'</span></span>
- <span data-ttu-id="57132-1171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1171">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1172">'Razor'</span></span>
- <span data-ttu-id="57132-1173">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1175">'Blazor'</span></span>
- <span data-ttu-id="57132-1176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1176">'Identity'</span></span>
- <span data-ttu-id="57132-1177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1177">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1178">'Razor'</span></span>
- <span data-ttu-id="57132-1179">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1179">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1181">'Blazor'</span></span>
- <span data-ttu-id="57132-1182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1182">'Identity'</span></span>
- <span data-ttu-id="57132-1183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1183">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1184">'Razor'</span></span>
- <span data-ttu-id="57132-1185">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1185">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1187">'Blazor'</span></span>
- <span data-ttu-id="57132-1188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1188">'Identity'</span></span>
- <span data-ttu-id="57132-1189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1189">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1190">'Razor'</span></span>
- <span data-ttu-id="57132-1191">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1193">'Blazor'</span></span>
- <span data-ttu-id="57132-1194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1194">'Identity'</span></span>
- <span data-ttu-id="57132-1195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1195">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1196">'Razor'</span></span>
- <span data-ttu-id="57132-1197">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1197">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1199">'Blazor'</span></span>
- <span data-ttu-id="57132-1200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1200">'Identity'</span></span>
- <span data-ttu-id="57132-1201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1201">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1202">'Razor'</span></span>
- <span data-ttu-id="57132-1203">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1205">'Blazor'</span></span>
- <span data-ttu-id="57132-1206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1206">'Identity'</span></span>
- <span data-ttu-id="57132-1207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1207">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1208">'Razor'</span></span>
- <span data-ttu-id="57132-1209">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1211">'Blazor'</span></span>
- <span data-ttu-id="57132-1212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1212">'Identity'</span></span>
- <span data-ttu-id="57132-1213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1213">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1214">'Razor'</span></span>
- <span data-ttu-id="57132-1215">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1217">'Blazor'</span></span>
- <span data-ttu-id="57132-1218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1218">'Identity'</span></span>
- <span data-ttu-id="57132-1219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1219">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1220">'Razor'</span></span>
- <span data-ttu-id="57132-1221">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1223">'Blazor'</span></span>
- <span data-ttu-id="57132-1224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1224">'Identity'</span></span>
- <span data-ttu-id="57132-1225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1225">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1226">'Razor'</span></span>
- <span data-ttu-id="57132-1227">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1229">'Blazor'</span></span>
- <span data-ttu-id="57132-1230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1230">'Identity'</span></span>
- <span data-ttu-id="57132-1231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1231">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1232">'Razor'</span></span>
- <span data-ttu-id="57132-1233">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1235">'Blazor'</span></span>
- <span data-ttu-id="57132-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1236">'Identity'</span></span>
- <span data-ttu-id="57132-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1238">'Razor'</span></span>
- <span data-ttu-id="57132-1239">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1241">'Blazor'</span></span>
- <span data-ttu-id="57132-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1242">'Identity'</span></span>
- <span data-ttu-id="57132-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1244">'Razor'</span></span>
- <span data-ttu-id="57132-1245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1245">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1246">---------- | | `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | mz        | 예   | 식을 찾습니다.    | | `[a-z]{2}`   | MZ        | 예   | 대/소문자를 구분하지 않습니다.    | | `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$`를 참조하세요. | | `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$`를 참조하세요. |</span><span class="sxs-lookup"><span data-stu-id="57132-1246">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="57132-1247">정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1247">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="57132-1248">가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1248">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="57132-1249">예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1249">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="57132-1250">제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1250">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="57132-1251">알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전에서 전달되는 제약 조건도 정규식으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1251">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="57132-1252">알려진 제약 조건 중 하나와 일치하지 않는 템플릿 내에서 전달되는 제약 조건은 정규식으로 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1252">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="57132-1253">사용자 지정 경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-1253">Custom route constraints</span></span>

<span data-ttu-id="57132-1254"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1254">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="57132-1255">`IRouteConstraint` 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 <xref:System.Web.Routing.IRouteConstraint.Match*>가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1255">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="57132-1256">사용자 지정 경로 제약 조건은 거의 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1256">Custom route constraints are rarely needed.</span></span> <span data-ttu-id="57132-1257">사용자 지정 경로 제약 조건을 구현하기 전에 모델 바인딩과 같은 다른 방식을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1257">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="57132-1258">ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 폴더는 제약 조건을 만드는 좋은 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1258">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span> <span data-ttu-id="57132-1259">예를 들어 [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1259">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>

<span data-ttu-id="57132-1260">사용자 지정 `IRouteConstraint`를 사용하려면 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1260">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span> <span data-ttu-id="57132-1261">`ConstraintMap`은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 `IRouteConstraint` 구현과 매핑하는 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1261">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="57132-1262">`Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 `ConstraintMap`을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1262">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="57132-1263">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="57132-1263">For example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="57132-1264">위의 제약 조건은 다음 코드에서 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1264">The preceding constraint is applied in the following code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="57132-1265">`MyCustomConstraint`를 구현하면 경로 매개 변수에 `0`이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1265">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="57132-1266">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="57132-1266">The preceding code:</span></span>

* <span data-ttu-id="57132-1267">경로의 `{id}` 세그먼트에 `0`을 사용하지 못하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1267">Prevents `0` in the `{id}` segment of the route.</span></span>
* <span data-ttu-id="57132-1268">사용자 지정 제약 조건을 구현하는 기본 예제를 제공하기 위해 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1268">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="57132-1269">프로덕션 앱에 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1269">It should not be used in a production app.</span></span>

<span data-ttu-id="57132-1270">다음 코드는 `0`이 포함된 `id`가 처리되지 않게 하는 더 나은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1270">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="57132-1271">위의 코드는 `MyCustomConstraint` 접근 방식과 비교하면 다음과 같은 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1271">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="57132-1272">사용자 지정 제약 조건이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1272">It doesn't require a custom constraint.</span></span>
* <span data-ttu-id="57132-1273">경로 매개 변수에 `0`이 포함된 경우 더 자세한 설명이 포함된 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1273">It returns a more descriptive error when the route parameter includes `0`.</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="57132-1274">매개 변수 변환기 참조</span><span class="sxs-lookup"><span data-stu-id="57132-1274">Parameter transformer reference</span></span>

<span data-ttu-id="57132-1275">매개 변수 변환기는:</span><span class="sxs-lookup"><span data-stu-id="57132-1275">Parameter transformers:</span></span>

* <span data-ttu-id="57132-1276"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>를 사용하여 링크를 생성할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1276">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span>
* <span data-ttu-id="57132-1277"><xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1277">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span>
* <span data-ttu-id="57132-1278"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1278">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="57132-1279">매개 변수의 경로 값을 가져와서 새 문자열 값으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1279">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="57132-1280">생성된 링크에서 변환된 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1280">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="57132-1281">예를 들어, `Url.Action(new { article = "MyTestArticle" })`을 사용하는 경로 패턴 `blog\{article:slugify}`의 사용자 지정 `slugify` 매개 변수 변환기는 `blog\my-test-article`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1281">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="57132-1282">다음 `IOutboundParameterTransformer` 구현을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1282">Consider the following `IOutboundParameterTransformer` implementation:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="57132-1283">경로 패턴에서 매개 변수 변환기를 사용하려면 `Startup.ConfigureServices`의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1283">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="57132-1284">ASP.NET Core Framework는 매개 변수 변화기를 사용하여 엔드포인트가 해결되는 URI를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1284">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="57132-1285">예를 들어 매개 변수 변환기는 `area`, `controller`, `action` 및 `page`와 일치하도록 사용되는 경로 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1285">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="57132-1286">위의 경로 템플릿을 사용하면 `SubscriptionManagementController.GetAll` 작업이 URI `/subscription-management/get-all`과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1286">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="57132-1287">매개 변수 변환기는 링크를 생성하는 데 사용되는 경로 값을 변경하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1287">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="57132-1288">예를 들어 `Url.Action("GetAll", "SubscriptionManagement")`는 `/subscription-management/get-all`을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1288">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="57132-1289">ASP.NET Core는 생성된 경로와 함께 매개 변수 변환기를 사용하기 위한 API 규칙을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1289">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span>

* <span data-ttu-id="57132-1290">이 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC 규칙은 앱의 모든 특성 경로에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1290">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="57132-1291">매개 변수 변환기는 특성 경로 토큰이 교체될 때 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1291">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="57132-1292">자세한 내용은 [매개 변수 변환기를 사용하여 토큰 교체 사용자 지정](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1292">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="57132-1293"> Pages에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1293"> Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span> <span data-ttu-id="57132-1294">이 규칙은 자동으로 검색된 모든 Razor Pages에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1294">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="57132-1295">매개 변수 변환기는 Razor Pages 경로의 폴더와 파일 이름 부분을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1295">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="57132-1296">자세한 내용은 [매개 변수 변환기를 사용하여 페이지 경로 사용자 지정](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1296">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="57132-1297">URL 생성 참조</span><span class="sxs-lookup"><span data-stu-id="57132-1297">URL generation reference</span></span>

<span data-ttu-id="57132-1298">이 단원에는 URL 생성에서 구현하는 알고리즘에 대한 참조가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1298">This section contains a reference for the algorithm implemented by URL generation.</span></span> <span data-ttu-id="57132-1299">실제로 URL 생성의 가장 복잡한 예제에서는 컨트롤러나 Razor Pages를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1299">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span> <span data-ttu-id="57132-1300">자세한 내용은 [routing in controllers](xref:mvc/controllers/routing)(컨트롤러의 라우팅)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1300">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span>

<span data-ttu-id="57132-1301">URL 생성 프로세스는 [LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 또는 이와 유사한 메서드를 호출하여 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1301">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> <span data-ttu-id="57132-1302">메서드에는 주소, 경로 값 집합 및 `HttpContext`의 현재 요청에 관한 정보(선택 사항)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1302">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>

<span data-ttu-id="57132-1303">첫 번째 단계에서는 주소를 사용하여 주소의 형식과 일치하는 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)을 사용하는 후보 엔드포인트 집합을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1303">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>

<span data-ttu-id="57132-1304">주소 체계에 따라 후보 집합을 찾으면 URL 생성 작업이 성공할 때까지 엔드포인트가 순서 지정되고 반복적으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1304">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span> <span data-ttu-id="57132-1305">URL 생성에서는 모호성을 확인하지 **않으며**, 반환되는 첫 번째 결과가 최종 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1305">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="57132-1306">로깅을 사용하여 URL 생성 문제 해결</span><span class="sxs-lookup"><span data-stu-id="57132-1306">Troubleshooting URL generation with logging</span></span>

<span data-ttu-id="57132-1307">URL 생성 문제를 해결하는 첫 번째 단계는 `Microsoft.AspNetCore.Routing`의 로깅 수준을 `TRACE`로 설정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1307">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span> <span data-ttu-id="57132-1308">`LinkGenerator`는 문제 해결에 유용할 수 있는 처리에 관한 여러 세부 정보를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1308">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span>

<span data-ttu-id="57132-1309">URL 생성에 대한 자세한 내용은 [URL 생성 참조](#ugr)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1309">See [URL generation reference](#ugr) for details on URL generation.</span></span>

### <a name="addresses"></a><span data-ttu-id="57132-1310">주소</span><span class="sxs-lookup"><span data-stu-id="57132-1310">Addresses</span></span>

<span data-ttu-id="57132-1311">주소는 URL 생성에서 링크 생성기의 호출을 후보 엔드포인트 집합에 바인딩하는 데 사용되는 개념입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1311">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span>

<span data-ttu-id="57132-1312">주소는 기본적으로 다음 두 가지 구현이 함께 제공되는 확장 가능한 개념입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1312">Addresses are an extensible concept that come with two implementations by default:</span></span>

* <span data-ttu-id="57132-1313">‘엔드포인트 이름’(`string`)을 주소로 사용:</span><span class="sxs-lookup"><span data-stu-id="57132-1313">Using *endpoint name* (`string`) as the address:</span></span>
    * <span data-ttu-id="57132-1314">MVC의 경로 이름과 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1314">Provides similar functionality to MVC's route name.</span></span>
    * <span data-ttu-id="57132-1315"><xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 메타데이터 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1315">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>
    * <span data-ttu-id="57132-1316">등록된 모든 엔드포인트의 메타데이터와 제공된 문자열을 비교하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1316">Resolves the provided string against the metadata of all registered endpoints.</span></span>
    * <span data-ttu-id="57132-1317">여러 엔드포인트에서 같은 이름을 사용하는 경우 시작 시 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1317">Throws an exception on startup if multiple endpoints use the same name.</span></span>
    * <span data-ttu-id="57132-1318">컨트롤러 및 Razor Pages 외의 일반적인 용도에 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1318">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>
* <span data-ttu-id="57132-1319">‘경로 값’(<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>)을 주소로 사용:</span><span class="sxs-lookup"><span data-stu-id="57132-1319">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>
    * <span data-ttu-id="57132-1320">컨트롤러 및 Razor Pages 레거시 URL 생성과 비슷한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1320">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>
    * <span data-ttu-id="57132-1321">확장 및 디버그하기가 매우 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1321">Very complex to extend and debug.</span></span>
    * <span data-ttu-id="57132-1322">`IUrlHelper`, 태그 도우미, HTML 도우미, 작업 결과 등에서 사용하는 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1322">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

<span data-ttu-id="57132-1323">주소 체계의 역할은 다음과 같은 임의 조건에 따라 주소와 일치하는 엔드포인트 사이를 연결하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1323">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>

* <span data-ttu-id="57132-1324">엔드포인트 이름 체계에서 기본 사전 조회를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1324">The endpoint name scheme performs a basic dictionary lookup.</span></span>
* <span data-ttu-id="57132-1325">경로 값 체계에는 알고리즘의 복잡한 최적 하위 집합이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1325">The route values scheme has a complex best subset of set algorithm.</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="57132-1326">앰비언트 값 및 명시적 값</span><span class="sxs-lookup"><span data-stu-id="57132-1326">Ambient values and explicit values</span></span>

<span data-ttu-id="57132-1327">현재 요청에서 라우팅은 현재 요청의 경로 값 `HttpContext.Request.RouteValues`에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1327">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="57132-1328">현재 요청과 연결된 값을 **앰비언트 값**이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1328">The values associated with the current request are referred to as the **ambient values**.</span></span> <span data-ttu-id="57132-1329">명확하게 하도록 설명서에서는 메서드에 전달된 경로 값을 **명시적 값**이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1329">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span>

<span data-ttu-id="57132-1330">다음 예제에서는 앰비언트 값과 명시적 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1330">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="57132-1331">현재 요청의 앰비언트 값과 명시적 값 `{ id = 17, }`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1331">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="57132-1332">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="57132-1332">The preceding code:</span></span>

* <span data-ttu-id="57132-1333">`/Widget/Index/17`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1333">Returns `/Widget/Index/17`</span></span>
* <span data-ttu-id="57132-1334">[DI](xref:fundamentals/dependency-injection)를 통해 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1334">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="57132-1335">다음 코드는 앰비언트 값과 `{ controller = "Home", action = "Subscribe", id = 17, }`을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1335">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="57132-1336">앞의 메서드는 `/Home/Subscribe/17`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1336">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="57132-1337">`WidgetController`의 다음 코드는 `/Widget/Subscribe/17`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1337">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="57132-1338">다음 코드는 현재 요청의 앰비언트 값과 명시적 값 `{ action = "Edit", id = 17, }`을 컨트롤러에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1338">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="57132-1339">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="57132-1339">In the preceding code:</span></span>

* <span data-ttu-id="57132-1340">`/Gadget/Edit/17`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1340">`/Gadget/Edit/17` is returned.</span></span>
* <span data-ttu-id="57132-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>는 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="57132-1342">작업 메서드의 절대 경로가 포함된 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1342">generates a URL with an absolute path for an action method.</span></span> <span data-ttu-id="57132-1343">URL에는 지정된 `action` 이름과 `route` 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1343">The URL contains the specified `action` name and `route` values.</span></span>

<span data-ttu-id="57132-1344">다음 코드에서는 현재 요청의 앰비언트 값과 명시적 값 `{ page = "./Edit, id = 17, }`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1344">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="57132-1345">앞의 코드에서는 Razor 편집 페이지에 다음 페이지 지시문이 포함된 경우 `url`을 `/Edit/17`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1345">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="57132-1346">편집 페이지에 `"{id:int}"` 경로 템플릿이 포함되어 있지 않으면 `url`은 `/Edit?id=17`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1346">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>

<span data-ttu-id="57132-1347">MVC의 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 동작은 여기에 설명된 규칙 외에도 복잡성 계층을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1347">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span>

* <span data-ttu-id="57132-1348">`IUrlHelper`는 항상 현재 요청의 경로 값을 앰비언트 값으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1348">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
* <span data-ttu-id="57132-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*)은 개발자가 재정의하는 경우 외에는 항상 현재 `action` 및 `controller` 경로 값을 명시적 값으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span>
* <span data-ttu-id="57132-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)는 재정의되는 경우 외에는 항상 현재 `page` 경로 값을 명시적 값으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <!--by the user-->
* <span data-ttu-id="57132-1351">`IUrlHelper.Page`는 재정의되는 경우 외에는 항상 현재 `handler` 경로 값을 명시적 값 `null`로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1351">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

<span data-ttu-id="57132-1352">MVC가 자체 규칙을 따르지 않기 때문에 앰비언트 값의 동작 세부 정보에 사용자가 놀라는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1352">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span> <span data-ttu-id="57132-1353">기록 및 호환성을 위해 `action`, `controller`, `page`, `handler` 등의 특정 경로 값에는 고유한 특수 사례 동작이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1353">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span>

<span data-ttu-id="57132-1354">`LinkGenerator.GetPathByAction` 및 `LinkGenerator.GetPathByPage`에서 제공하는 동일한 기능에서는 호환성을 위해 `IUrlHelper`의 이러한 변칙을 복제합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1354">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="57132-1355">URL 생성 프로세스</span><span class="sxs-lookup"><span data-stu-id="57132-1355">URL generation process</span></span>

<span data-ttu-id="57132-1356">후보 엔드포인트 집합을 찾으면 URL 생성 알고리즘은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1356">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span>

* <span data-ttu-id="57132-1357">엔드포인트를 반복적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1357">Processes the endpoints iteratively.</span></span>
* <span data-ttu-id="57132-1358">첫 번째 성공적인 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1358">Returns the first successful result.</span></span>

<span data-ttu-id="57132-1359">이 프로세스의 첫 번째 단계를 **경로 값 무효화**라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1359">The first step in this process is called **route value invalidation**.</span></span>  <span data-ttu-id="57132-1360">경로 값 무효화는 라우팅에서 앰비언트 값의 어떤 경로 값을 사용하고 무시할지 결정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1360">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="57132-1361">각 앰비언트 값이 고려되고 명시적 값과 결합되거나 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1361">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="57132-1362">앰비언트 값의 역할을 알아보려면 일반적인 사례에서 애플리케이션 개발자 입력을 저장하려고 하는 점을 고려하면 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1362">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span> <span data-ttu-id="57132-1363">일반적으로 앰비언트 값이 유용한 시나리오는 MVC와 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1363">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span>

* <span data-ttu-id="57132-1364">동일한 컨트롤러의 다른 작업에 연결하는 경우에는 컨트롤러 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1364">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>
* <span data-ttu-id="57132-1365">같은 영역의 다른 컨트롤러에 연결하는 경우 영역 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1365">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span>
* <span data-ttu-id="57132-1366">동일한 작업 메서드에 연결하는 경우 경로 값을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1366">When linking to the same action method, route values don't need to be specified.</span></span>
* <span data-ttu-id="57132-1367">앱의 다른 부분에 연결하는 경우 앱의 해당 부분에서 의미가 없는 경로 값을 전달하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1367">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span>

<span data-ttu-id="57132-1368">`null`을 반환하는 `LinkGenerator` 또는 `IUrlHelper`를 호출하면 일반적으로 이해되지 않는 경로 값 무효화가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1368">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span> <span data-ttu-id="57132-1369">경로 값 무효화 문제를 해결하려면 더 많은 경로 값을 명시적으로 지정하여 문제가 해결되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1369">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span>

<span data-ttu-id="57132-1370">경로 값 무효화는 앱의 URL 체계가 계층이 왼쪽에서 오른쪽으로 형성된 계층 구조라고 가정하고 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1370">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="57132-1371">기본 컨트롤러 경로 템플릿 `{controller}/{action}/{id?}`를 사용하여 실제 작동 방법을 직관적으로 파악해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1371">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="57132-1372">값을 **변경**하면 오른쪽에 표시되는 경로 값이 모두 **무효화**됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1372">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="57132-1373">이는 계층 구조에 관한 가정이 반영된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1373">This reflects the assumption about hierarchy.</span></span> <span data-ttu-id="57132-1374">앱에 `id`의 앰비언트 값이 있고 작업에서 `controller`에 대해 다른 값을 지정하는 경우:</span><span class="sxs-lookup"><span data-stu-id="57132-1374">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

* <span data-ttu-id="57132-1375">`{controller}`가 `{id?}`의 왼쪽에 있으므로 `id`가 다시 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1375">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span>

<span data-ttu-id="57132-1376">이 원칙을 보여 주는 몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1376">Some examples demonstrating this principle:</span></span>

* <span data-ttu-id="57132-1377">명시적 값에 `id`의 값이 포함된 경우 `id`의 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1377">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="57132-1378">`controller` 및 `action`의 앰비언트 값이 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1378">The ambient values for `controller` and `action` can be used.</span></span>
* <span data-ttu-id="57132-1379">명시적 값에 `action`의 값이 포함된 경우 `action`의 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1379">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span> <span data-ttu-id="57132-1380">`controller`의 앰비언트 값이 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1380">The ambient values for `controller` can be used.</span></span> <span data-ttu-id="57132-1381">`action`의 명시적 값이 `action`의 앰비언트 값과 다른 경우 `id` 값은 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1381">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>  <span data-ttu-id="57132-1382">`action`의 명시적 값이 `action`의 앰비언트 값과 같으면 `id` 값이 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1382">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>
* <span data-ttu-id="57132-1383">명시적 값에 `controller`의 값이 포함된 경우 `controller`의 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1383">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="57132-1384">`controller`의 명시적 값이 `controller`의 앰비언트 값과 다른 경우 `action` 및 `id` 값은 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1384">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="57132-1385">`controller`의 명시적 값이 `controller`의 앰비언트 값과 같으면 `action` 및 `id` 값이 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1385">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span>

<span data-ttu-id="57132-1386">이 프로세스는 특성 경로와 전용 규칙 기반 경로가 있으면 더 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1386">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span> <span data-ttu-id="57132-1387">`{controller}/{action}/{id?}`와 같은 컨트롤러 규칙 기반 경로는 경로 매개 변수를 사용하여 계층 구조를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1387">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="57132-1388">컨트롤러 및 Razor Pages에 대한 [전용 규칙 기반 경로](xref:mvc/controllers/routing#dcr) 및 [특성 경로](xref:mvc/controllers/routing#ar)의 경우:</span><span class="sxs-lookup"><span data-stu-id="57132-1388">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

* <span data-ttu-id="57132-1389">경로 값의 계층 구조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1389">There is a hierarchy of route values.</span></span>
* <span data-ttu-id="57132-1390">템플릿에는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1390">They don't appear in the template.</span></span>

<span data-ttu-id="57132-1391">이러한 경우 URL 생성에서 **필수 값** 개념을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1391">For these cases, URL generation defines the **required values** concept.</span></span> <span data-ttu-id="57132-1392">컨트롤러 및 Razor Pages에서 만든 엔드포인트에는 경로 값 무효화가 작동할 수 있도록 필수 값이 지정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1392">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span>

<span data-ttu-id="57132-1393">경로 값 무효화 알고리즘을 자세히 설명하면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1393">The route value invalidation algorithm in detail:</span></span>

* <span data-ttu-id="57132-1394">필요 값 이름을 경로 매개 변수와 결합한 다음 왼쪽에서 오른쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1394">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>
* <span data-ttu-id="57132-1395">각 매개 변수에 대해 앰비언트 값과 명시적 값을 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1395">For each parameter, the ambient value and explicit value are compared:</span></span>
    * <span data-ttu-id="57132-1396">앰비언트 값과 명시적 값이 같으면 프로세스가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1396">If the ambient value and explicit value are the same, the process continues.</span></span>
    * <span data-ttu-id="57132-1397">앰비언트 값이 있고 명시적 값이 없으면 URL을 생성할 때 앰비언트 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1397">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>
    * <span data-ttu-id="57132-1398">앰비언트 값이 없고 명시적 값이 있으면 앰비언트 값과 모든 후속 앰비언트 값을 거부합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1398">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>
    * <span data-ttu-id="57132-1399">앰비언트 값과 명시적 값이 있고 두 값이 다른 경우 앰비언트 값과 모든 후속 앰비언트 값을 거부합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1399">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="57132-1400">이제 URL 생성 작업에서 경로 제약 조건을 평가할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1400">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="57132-1401">허용되는 값 집합이 제약 조건에 제공되는 매개 변수 기본값과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1401">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span> <span data-ttu-id="57132-1402">제약 조건이 모두 통과되면 작업이 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1402">If the constraints all pass, the operation continues.</span></span>

<span data-ttu-id="57132-1403">다음으로 **허용되는 값**을 사용하여 경로 템플릿을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1403">Next, the **accepted values** can be used to expand the route template.</span></span> <span data-ttu-id="57132-1404">경로 템플릿은 다음과 같이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1404">The route template is processed:</span></span>

* <span data-ttu-id="57132-1405">왼쪽에서 오른쪽으로</span><span class="sxs-lookup"><span data-stu-id="57132-1405">From left-to-right.</span></span>
* <span data-ttu-id="57132-1406">각 매개 변수의 허용되는 값이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1406">Each parameter has its accepted value substituted.</span></span>
* <span data-ttu-id="57132-1407">다음과 같은 특수 사례의 경우:</span><span class="sxs-lookup"><span data-stu-id="57132-1407">With the following special cases:</span></span>
  * <span data-ttu-id="57132-1408">허용되는 값에 누락된 값이 있고 매개 변수에 기본값이 있으면 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1408">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>
  * <span data-ttu-id="57132-1409">허용되는 값에 누락된 값이 있고 매개 변수가 선택 사항이면 처리가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1409">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>
  * <span data-ttu-id="57132-1410">누락된 선택적 매개 변수 오른쪽의 경로 매개 변수에 값이 있으면 작업이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1410">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="57132-1411">연속된 기본값 매개 변수 및 선택적 매개 변수는 가능하면 축소됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1411">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="57132-1412">명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1412">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="57132-1413">다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1413">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="57132-1414">앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="57132-1414">Ambient Values</span></span>                     | <span data-ttu-id="57132-1415">명시적 값</span><span class="sxs-lookup"><span data-stu-id="57132-1415">Explicit Values</span></span>                        | <span data-ttu-id="57132-1416">결과</span><span class="sxs-lookup"><span data-stu-id="57132-1416">Result</span></span>                  |
| ---
<span data-ttu-id="57132-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1418">'Blazor'</span></span>
- <span data-ttu-id="57132-1419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1419">'Identity'</span></span>
- <span data-ttu-id="57132-1420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1420">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1421">'Razor'</span></span>
- <span data-ttu-id="57132-1422">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1424">'Blazor'</span></span>
- <span data-ttu-id="57132-1425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1425">'Identity'</span></span>
- <span data-ttu-id="57132-1426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1426">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1427">'Razor'</span></span>
- <span data-ttu-id="57132-1428">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1430">'Blazor'</span></span>
- <span data-ttu-id="57132-1431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1431">'Identity'</span></span>
- <span data-ttu-id="57132-1432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1432">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1433">'Razor'</span></span>
- <span data-ttu-id="57132-1434">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1436">'Blazor'</span></span>
- <span data-ttu-id="57132-1437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1437">'Identity'</span></span>
- <span data-ttu-id="57132-1438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1438">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1439">'Razor'</span></span>
- <span data-ttu-id="57132-1440">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1442">'Blazor'</span></span>
- <span data-ttu-id="57132-1443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1443">'Identity'</span></span>
- <span data-ttu-id="57132-1444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1444">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1445">'Razor'</span></span>
- <span data-ttu-id="57132-1446">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1448">'Blazor'</span></span>
- <span data-ttu-id="57132-1449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1449">'Identity'</span></span>
- <span data-ttu-id="57132-1450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1450">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1451">'Razor'</span></span>
- <span data-ttu-id="57132-1452">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1454">'Blazor'</span></span>
- <span data-ttu-id="57132-1455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1455">'Identity'</span></span>
- <span data-ttu-id="57132-1456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1456">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1457">'Razor'</span></span>
- <span data-ttu-id="57132-1458">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1460">'Blazor'</span></span>
- <span data-ttu-id="57132-1461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1461">'Identity'</span></span>
- <span data-ttu-id="57132-1462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1462">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1463">'Razor'</span></span>
- <span data-ttu-id="57132-1464">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1466">'Blazor'</span></span>
- <span data-ttu-id="57132-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1467">'Identity'</span></span>
- <span data-ttu-id="57132-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1469">'Razor'</span></span>
- <span data-ttu-id="57132-1470">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1472">'Blazor'</span></span>
- <span data-ttu-id="57132-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1473">'Identity'</span></span>
- <span data-ttu-id="57132-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1475">'Razor'</span></span>
- <span data-ttu-id="57132-1476">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1478">'Blazor'</span></span>
- <span data-ttu-id="57132-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1479">'Identity'</span></span>
- <span data-ttu-id="57132-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1481">'Razor'</span></span>
- <span data-ttu-id="57132-1482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1484">'Blazor'</span></span>
- <span data-ttu-id="57132-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1485">'Identity'</span></span>
- <span data-ttu-id="57132-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1487">'Razor'</span></span>
- <span data-ttu-id="57132-1488">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1490">'Blazor'</span></span>
- <span data-ttu-id="57132-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1491">'Identity'</span></span>
- <span data-ttu-id="57132-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1493">'Razor'</span></span>
- <span data-ttu-id="57132-1494">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1496">'Blazor'</span></span>
- <span data-ttu-id="57132-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1497">'Identity'</span></span>
- <span data-ttu-id="57132-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1499">'Razor'</span></span>
- <span data-ttu-id="57132-1500">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1502">'Blazor'</span></span>
- <span data-ttu-id="57132-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1503">'Identity'</span></span>
- <span data-ttu-id="57132-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1505">'Razor'</span></span>
- <span data-ttu-id="57132-1506">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1506">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1508">'Blazor'</span></span>
- <span data-ttu-id="57132-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1509">'Identity'</span></span>
- <span data-ttu-id="57132-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1511">'Razor'</span></span>
- <span data-ttu-id="57132-1512">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1514">'Blazor'</span></span>
- <span data-ttu-id="57132-1515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1515">'Identity'</span></span>
- <span data-ttu-id="57132-1516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1516">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1517">'Razor'</span></span>
- <span data-ttu-id="57132-1518">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1520">'Blazor'</span></span>
- <span data-ttu-id="57132-1521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1521">'Identity'</span></span>
- <span data-ttu-id="57132-1522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1522">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1523">'Razor'</span></span>
- <span data-ttu-id="57132-1524">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1526">'Blazor'</span></span>
- <span data-ttu-id="57132-1527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1527">'Identity'</span></span>
- <span data-ttu-id="57132-1528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1528">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1529">'Razor'</span></span>
- <span data-ttu-id="57132-1530">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1532">'Blazor'</span></span>
- <span data-ttu-id="57132-1533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1533">'Identity'</span></span>
- <span data-ttu-id="57132-1534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1534">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1535">'Razor'</span></span>
- <span data-ttu-id="57132-1536">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1538">'Blazor'</span></span>
- <span data-ttu-id="57132-1539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1539">'Identity'</span></span>
- <span data-ttu-id="57132-1540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1540">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1541">'Razor'</span></span>
- <span data-ttu-id="57132-1542">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1544">'Blazor'</span></span>
- <span data-ttu-id="57132-1545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1545">'Identity'</span></span>
- <span data-ttu-id="57132-1546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1546">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1547">'Razor'</span></span>
- <span data-ttu-id="57132-1548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1550">'Blazor'</span></span>
- <span data-ttu-id="57132-1551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1551">'Identity'</span></span>
- <span data-ttu-id="57132-1552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1552">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1553">'Razor'</span></span>
- <span data-ttu-id="57132-1554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1556">'Blazor'</span></span>
- <span data-ttu-id="57132-1557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1557">'Identity'</span></span>
- <span data-ttu-id="57132-1558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1558">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1559">'Razor'</span></span>
- <span data-ttu-id="57132-1560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1562">'Blazor'</span></span>
- <span data-ttu-id="57132-1563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1563">'Identity'</span></span>
- <span data-ttu-id="57132-1564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1564">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1565">'Razor'</span></span>
- <span data-ttu-id="57132-1566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1568">'Blazor'</span></span>
- <span data-ttu-id="57132-1569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1569">'Identity'</span></span>
- <span data-ttu-id="57132-1570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1570">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1571">'Razor'</span></span>
- <span data-ttu-id="57132-1572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1574">'Blazor'</span></span>
- <span data-ttu-id="57132-1575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1575">'Identity'</span></span>
- <span data-ttu-id="57132-1576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1576">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1577">'Razor'</span></span>
- <span data-ttu-id="57132-1578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1580">'Blazor'</span></span>
- <span data-ttu-id="57132-1581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1581">'Identity'</span></span>
- <span data-ttu-id="57132-1582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1582">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1583">'Razor'</span></span>
- <span data-ttu-id="57132-1584">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1586">'Blazor'</span></span>
- <span data-ttu-id="57132-1587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1587">'Identity'</span></span>
- <span data-ttu-id="57132-1588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1588">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1589">'Razor'</span></span>
- <span data-ttu-id="57132-1590">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1592">'Blazor'</span></span>
- <span data-ttu-id="57132-1593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1593">'Identity'</span></span>
- <span data-ttu-id="57132-1594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1594">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1595">'Razor'</span></span>
- <span data-ttu-id="57132-1596">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1598">'Blazor'</span></span>
- <span data-ttu-id="57132-1599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1599">'Identity'</span></span>
- <span data-ttu-id="57132-1600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1600">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1601">'Razor'</span></span>
- <span data-ttu-id="57132-1602">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1604">'Blazor'</span></span>
- <span data-ttu-id="57132-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1605">'Identity'</span></span>
- <span data-ttu-id="57132-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1607">'Razor'</span></span>
- <span data-ttu-id="57132-1608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1608">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1610">'Blazor'</span></span>
- <span data-ttu-id="57132-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1611">'Identity'</span></span>
- <span data-ttu-id="57132-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1613">'Razor'</span></span>
- <span data-ttu-id="57132-1614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1616">'Blazor'</span></span>
- <span data-ttu-id="57132-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1617">'Identity'</span></span>
- <span data-ttu-id="57132-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1619">'Razor'</span></span>
- <span data-ttu-id="57132-1620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1622">'Blazor'</span></span>
- <span data-ttu-id="57132-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1623">'Identity'</span></span>
- <span data-ttu-id="57132-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1625">'Razor'</span></span>
- <span data-ttu-id="57132-1626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1628">'Blazor'</span></span>
- <span data-ttu-id="57132-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1629">'Identity'</span></span>
- <span data-ttu-id="57132-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1631">'Razor'</span></span>
- <span data-ttu-id="57132-1632">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1634">'Blazor'</span></span>
- <span data-ttu-id="57132-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1635">'Identity'</span></span>
- <span data-ttu-id="57132-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1637">'Razor'</span></span>
- <span data-ttu-id="57132-1638">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1640">'Blazor'</span></span>
- <span data-ttu-id="57132-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1641">'Identity'</span></span>
- <span data-ttu-id="57132-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1643">'Razor'</span></span>
- <span data-ttu-id="57132-1644">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1646">'Blazor'</span></span>
- <span data-ttu-id="57132-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1647">'Identity'</span></span>
- <span data-ttu-id="57132-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1649">'Razor'</span></span>
- <span data-ttu-id="57132-1650">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1652">'Blazor'</span></span>
- <span data-ttu-id="57132-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1653">'Identity'</span></span>
- <span data-ttu-id="57132-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1655">'Razor'</span></span>
- <span data-ttu-id="57132-1656">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1658">'Blazor'</span></span>
- <span data-ttu-id="57132-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1659">'Identity'</span></span>
- <span data-ttu-id="57132-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1661">'Razor'</span></span>
- <span data-ttu-id="57132-1662">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1663">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="57132-1663">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="57132-1664">경로 값 무효화 문제</span><span class="sxs-lookup"><span data-stu-id="57132-1664">Problems with route value invalidation</span></span>

<span data-ttu-id="57132-1665">ASP.NET Core 3.0부터 이전 ASP.NET Core 버전에서 사용되는 일부 URL 생성 체계가 URL 생성에서 잘 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1665">As of ASP.NET Core 3.0, some URL generation schemes used in earlier ASP.NET Core versions don't work well with URL generation.</span></span> <span data-ttu-id="57132-1666">ASP.NET Core 팀은 향후 릴리스에서 이러한 요구를 해결하는 기능을 추가할 계획입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1666">The ASP.NET Core team plans to add features to address these needs in a future release.</span></span> <span data-ttu-id="57132-1667">지금은 레거시 라우팅을 사용하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1667">For now the best solution is to use legacy routing.</span></span>

<span data-ttu-id="57132-1668">다음 코드에서는 라우팅에서 지원하지 않는 URL 생성 체계의 예제를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1668">The following code shows an example of a URL generation scheme that's not supported by routing.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="57132-1669">위의 코드에서 `culture` 경로 매개 변수는 지역화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1669">In the preceding code, the `culture` route parameter is used for localization.</span></span> <span data-ttu-id="57132-1670">`culture` 매개 변수가 앰비언트 값으로 항상 허용되게 하려는 것이 목표입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1670">The desire is to have the `culture` parameter always accepted as an ambient value.</span></span> <span data-ttu-id="57132-1671">그러나 `culture` 매개 변수는 필수 값이 작동하는 방식 때문에 앰비언트 값으로 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1671">However, the `culture` parameter is not accepted as an ambient value because of the way required values work:</span></span>

* <span data-ttu-id="57132-1672">`"default"` 경로 템플릿에서 `culture` 경로 매개 변수는 `controller`의 왼쪽에 있으므로 `controller`를 변경해도 `culture`가 무효화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1672">In the `"default"` route template, the `culture` route parameter is to the left of `controller`, so changes to `controller` won't invalidate `culture`.</span></span>
* <span data-ttu-id="57132-1673">`"blog"` 경로 템플릿에서 `culture` 경로 매개 변수는 필수 값에 표시되는 `controller`의 오른쪽에 있는 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1673">In the `"blog"` route template, the `culture` route parameter is considered to be to the right of `controller`, which appears in the required values.</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="57132-1674">엔드포인트 메타데이터 구성</span><span class="sxs-lookup"><span data-stu-id="57132-1674">Configuring endpoint metadata</span></span>

<span data-ttu-id="57132-1675">다음 링크는 엔드포인트 메타데이터를 구성하는 방법에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1675">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="57132-1676">엔드포인트 라우팅을 사용하여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="57132-1676">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="57132-1677">사용자 지정 `[MinimumAgeAuthorize]` 특성을 사용하는 [IAuthorizationPolicyProvider 샘플](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="57132-1677">[IAuthorizationPolicyProvider sample](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="57132-1678">[[권한 부여] 특성으로 인증 테스트](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="57132-1678">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="57132-1679">[[권한 부여] 특성을 갖는 체계 선택](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="57132-1679">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="57132-1680">[[권한 부여] 특성을 사용하여 정책 적용](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="57132-1680">[Applying policies using the [Authorize] attribute](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="57132-1681">RequireHost가 있는 경로의 호스트 일치</span><span class="sxs-lookup"><span data-stu-id="57132-1681">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="57132-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>는 지정된 호스트가 필요한 경로에 제약 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="57132-1683">`RequireHost` 또는 [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) 매개 변수는 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1683">The `RequireHost` or [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parameter can be:</span></span>

* <span data-ttu-id="57132-1684">호스트: `www.domain.com`(아무 포트에서나 `www.domain.com`과 일치)</span><span class="sxs-lookup"><span data-stu-id="57132-1684">Host: `www.domain.com`, matches `www.domain.com` with any port.</span></span>
* <span data-ttu-id="57132-1685">와일드카드가 있는 호스트: `*.domain.com`(아무 포트에서나 `www.domain.com`, `subdomain.domain.com` 또는 `www.subdomain.domain.com`과 일치)</span><span class="sxs-lookup"><span data-stu-id="57132-1685">Host with wildcard: `*.domain.com`, matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port.</span></span>
* <span data-ttu-id="57132-1686">포트: `*:5000`(아무 호스트에서나 포트 5000과 일치)</span><span class="sxs-lookup"><span data-stu-id="57132-1686">Port: `*:5000`, matches port 5000 with any host.</span></span>
* <span data-ttu-id="57132-1687">호스트 및 포트: `www.domain.com:5000` 또는 `*.domain.com:5000`(호스트 및 포트와 일치)</span><span class="sxs-lookup"><span data-stu-id="57132-1687">Host and port: `www.domain.com:5000` or `*.domain.com:5000`, matches host and port.</span></span>

<span data-ttu-id="57132-1688">`RequireHost` 또는 `[Host]`를 사용하여 여러 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1688">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="57132-1689">제약 조건은 모든 매개 변수에 유효한 호스트와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1689">The constraint  matches hosts valid for any of the parameters.</span></span> <span data-ttu-id="57132-1690">예를 들어 `[Host("domain.com", "*.domain.com")]`은 `domain.com`, `www.domain.com` 및 `subdomain.domain.com`과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1690">For example, `[Host("domain.com", "*.domain.com")]` matches `domain.com`, `www.domain.com`, and `subdomain.domain.com`.</span></span>

<span data-ttu-id="57132-1691">다음 코드는 `RequireHost`를 사용하여 경로상에 있는 지정된 호스트를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1691">The following code uses `RequireHost` to require the specified host on the route:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="57132-1692">다음 코드는 컨트롤러의 `[Host]` 특성을 사용하여 지정된 호스트를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1692">The following code uses the `[Host]` attribute on the controller to require any of the specified hosts:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="57132-1693">`[Host]` 특성이 컨트롤러 메서드와 작업 메서드에 모두 적용될 경우</span><span class="sxs-lookup"><span data-stu-id="57132-1693">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="57132-1694">작업의 특성이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1694">The attribute on the action is used.</span></span>
* <span data-ttu-id="57132-1695">컨트롤러의 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1695">The controller attribute is ignored.</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="57132-1696">라우팅의 성능 지침</span><span class="sxs-lookup"><span data-stu-id="57132-1696">Performance guidance for routing</span></span>

<span data-ttu-id="57132-1697">ASP.NET Core 3.0에서는 성능 향상을 위해 대부분의 라우팅이 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1697">Most of routing was updated in ASP.NET Core 3.0 to increase performance.</span></span>

<span data-ttu-id="57132-1698">앱에 성능 문제가 있는 경우 라우팅이 문제의 원인으로 의심받는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1698">When an app has performance problems, routing is often suspected as the problem.</span></span> <span data-ttu-id="57132-1699">라우팅이 의심받는 이유는 컨트롤러 및 Razor Pages 같은 프레임워크가 프레임워크 내에서 소요된 시간을 로깅 메시지로 보고하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1699">The reason routing is suspected is that frameworks like controllers and Razor Pages report the amount of time spent inside the framework in their logging messages.</span></span> <span data-ttu-id="57132-1700">컨트롤러에서 보고하는 시간과 요청의 총 시간 사이에 상당한 차이가 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="57132-1700">When there's a significant difference between the time reported by controllers and the total time of the request:</span></span>

* <span data-ttu-id="57132-1701">개발자는 문제의 원인인 앱 코드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1701">Developers eliminate their app code as the source of the problem.</span></span>
* <span data-ttu-id="57132-1702">일반적으로 라우팅이 원인이라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1702">It's common to assume routing is the cause.</span></span>

<span data-ttu-id="57132-1703">라우팅은 수천 개의 엔드포인트를 사용하여 성능을 테스트했습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1703">Routing is performance tested using thousands of endpoints.</span></span> <span data-ttu-id="57132-1704">일반적인 앱에서는 너무 크다고 성능 문제가 발생할 가능성은 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1704">It's unlikely that a typical app will encounter a performance problem just by being too large.</span></span> <span data-ttu-id="57132-1705">라우팅 성능이 저하되는 가장 일반적인 근본 원인은 일반적으로 잘못 동작하는 사용자 지정 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1705">The most common root cause of slow routing performance is usually a badly-behaving custom middleware.</span></span>

<span data-ttu-id="57132-1706">다음 코드 샘플에서는 지연의 원인을 좁히기 위한 기본 기술을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1706">This following code sample demonstrates a basic technique for narrowing down the source of delay:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="57132-1707">종료 시간 라우팅:</span><span class="sxs-lookup"><span data-stu-id="57132-1707">To time routing:</span></span>

* <span data-ttu-id="57132-1708">앞의 코드에 표시된 타이밍 미들웨어의 복사본을 각 미들웨어에 인터리빙합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1708">Interleave each middleware with a copy of the timing middleware shown in the preceding code.</span></span>
* <span data-ttu-id="57132-1709">고유 식별자를 추가하여 타이밍 데이터를 코드와 연관 짓습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1709">Add a unique identifier to correlate the timing data with the code.</span></span>

<span data-ttu-id="57132-1710">이것이 예를 들어 `10ms` 이상의 상당한 지연이 발생하는 경우 지연을 좁히는 기본적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1710">This is a basic way to narrow down the delay when it's significant, for example, more than `10ms`.</span></span>  <span data-ttu-id="57132-1711">`Time 1`에서 `Time 2`를 빼면 `UseRouting` 미들웨어 내에서 소요된 시간이 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1711">Subtracting `Time 2` from `Time 1` reports the time spent inside the `UseRouting` middleware.</span></span>

<span data-ttu-id="57132-1712">다음 코드에서는 앞의 타이밍 코드에 더욱 간결한 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1712">The following code uses a more compact approach to the preceding timing code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="57132-1713">비용이 많이 들 수 있는 라우팅 기능</span><span class="sxs-lookup"><span data-stu-id="57132-1713">Potentially expensive routing features</span></span>

<span data-ttu-id="57132-1714">다음 목록에서는 기본 경로 템플릿보다 비교적 비용이 많이 드는 라우팅 기능에 대한 몇 가지 유용한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1714">The following list provides some insight into routing features that are relatively expensive compared with basic route templates:</span></span>

* <span data-ttu-id="57132-1715">정규식: 정규식 작성은 복잡하거나 적은 양의 입력으로 실행 시간이 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1715">Regular expressions: It's possible to write regular expressions that are complex, or have long running time with a small amount of input.</span></span>

* <span data-ttu-id="57132-1716">복잡한 세그먼트(`{x}-{y}-{z}`):</span><span class="sxs-lookup"><span data-stu-id="57132-1716">Complex segments (`{x}-{y}-{z}`):</span></span> 
  * <span data-ttu-id="57132-1717">일반 URL 경로 세그먼트를 구문 분석하는 것보다 훨씬 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1717">Are significantly more expensive than parsing a regular URL path segment.</span></span>
  * <span data-ttu-id="57132-1718">더 많은 부분 문자열이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1718">Result in many more substrings being allocated.</span></span>
  * <span data-ttu-id="57132-1719">복잡한 세그먼트 논리는 ASP.NET Core 3.0 라우팅 성능 업데이트에서 업데이트되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1719">The complex segment logic was not updated in ASP.NET Core 3.0 routing performance update.</span></span>

* <span data-ttu-id="57132-1720">동기 데이터 액세스: 많은 복잡한 앱에서 라우팅의 일부로 데이터베이스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1720">Synchronous data access: Many complex apps have database access as part of their routing.</span></span> <span data-ttu-id="57132-1721">ASP.NET Core 2.2 및 이전 라우팅에서는 데이터베이스 액세스 라우팅을 지원하기 위한 적합한 확장 포인트를 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1721">ASP.NET Core 2.2 and earlier routing might not provide the right extensibility points to support database access routing.</span></span> <span data-ttu-id="57132-1722">예를 들어 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 및 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>는 동기식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1722">For example, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, and <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are synchronous.</span></span> <span data-ttu-id="57132-1723"><xref:Microsoft.AspNetCore.Routing.MatcherPolicy> 및 <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> 같은 확장 포인트는 비동기식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1723">Extensibility points such as <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> and <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> are asynchronous.</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="57132-1724">라이브러리 작성자를 위한 지침</span><span class="sxs-lookup"><span data-stu-id="57132-1724">Guidance for library authors</span></span>

<span data-ttu-id="57132-1725">이 단원에는 라우팅을 기반으로 빌드하는 라이브러리 작성자를 위한 지침이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1725">This section contains guidance for library authors building on top of routing.</span></span> <span data-ttu-id="57132-1726">이러한 세부 내용은 앱 개발자가 라우팅을 확장하는 라이브러리 및 프레임워크를 사용하는 좋은 환경을 갖추도록 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1726">These details are intended to ensure that app developers have a good experience using libraries and frameworks that extend routing.</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="57132-1727">엔드포인트 정의</span><span class="sxs-lookup"><span data-stu-id="57132-1727">Define endpoints</span></span>

<span data-ttu-id="57132-1728">URL 일치를 위해 라우팅을 사용하는 프레임워크를 만들려면 먼저 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>를 기반으로 빌드되는 사용자 환경을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1728">To create a framework that uses routing for URL matching, start by defining a user experience that builds on top of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>

<span data-ttu-id="57132-1729"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>를 기반으로 **빌드하세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1729">**DO** build on top of <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span> <span data-ttu-id="57132-1730">이렇게 하면 사용자는 혼동하지 않고 다른 ASP.NET Core 기능을 사용하여 프레임워크를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1730">This allows users to compose your framework with other ASP.NET Core features without confusion.</span></span> <span data-ttu-id="57132-1731">모든 ASP.NET Core 템플릿에는 라우팅이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1731">Every ASP.NET Core template includes routing.</span></span> <span data-ttu-id="57132-1732">라우팅이 있고 사용자에게 친숙하다고 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1732">Assume routing is present and familiar for users.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="57132-1733"><xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>를 구현하는 `MapMyFramework(...)` 호출에서 봉인된 구체적인 형식을 반환**하세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1733">**DO** return a sealed concrete type from a call to `MapMyFramework(...)` that implements <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>.</span></span> <span data-ttu-id="57132-1734">대부분의 프레임워크 `Map...` 메서드는 이 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1734">Most framework `Map...` methods follow this pattern.</span></span> <span data-ttu-id="57132-1735">`IEndpointConventionBuilder` 인터페이스:</span><span class="sxs-lookup"><span data-stu-id="57132-1735">The `IEndpointConventionBuilder` interface:</span></span>

* <span data-ttu-id="57132-1736">메타데이터를 작성 가능하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1736">Allows composability of metadata.</span></span>
* <span data-ttu-id="57132-1737">다양한 확장 메서드의 대상으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1737">Is targeted by a variety of extension methods.</span></span>

<span data-ttu-id="57132-1738">고유의 형식을 선언하면 작성기에 사용자 고유의 프레임워크 관련 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1738">Declaring your own type allows you to add your own framework-specific functionality to the builder.</span></span> <span data-ttu-id="57132-1739">프레임워크 선언된 작성기를 래핑하고 여기에 호출을 전달해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1739">It's ok to wrap a framework-declared builder and forward calls to it.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="57132-1740">사용자 고유의 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>를 작성하는 것이 **좋습니다**.</span><span class="sxs-lookup"><span data-stu-id="57132-1740">**CONSIDER** writing your own <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>.</span></span> <span data-ttu-id="57132-1741">`EndpointDataSource`는 엔드포인트 컬렉션을 선언하고 업데이트하기 위한 하위 수준 기본 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1741">`EndpointDataSource` is the low-level primitive for declaring and updating a collection of endpoints.</span></span> <span data-ttu-id="57132-1742">`EndpointDataSource`는 컨트롤러 및 Razor Pages에서 사용되는 강력한 API입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1742">`EndpointDataSource` is a powerful API used by controllers and Razor Pages.</span></span>

<span data-ttu-id="57132-1743">라우팅 테스트에는 업데이트되지 않는 데이터 원본의 [기본 예제](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1743">The routing tests have a [basic example](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) of a non-updating data source.</span></span>

<span data-ttu-id="57132-1744">기본적으로 `EndpointDataSource`를 등록하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1744">**DO NOT** attempt to register an `EndpointDataSource` by default.</span></span> <span data-ttu-id="57132-1745">프레임워크를 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>에 등록하도록 사용자에게 요구하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1745">Require users to register your framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="57132-1746">라우팅의 원리에 따르면 기본적으로 아무것도 포함되지 않으며 `UseEndpoints`가 엔드포인트를 등록하는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1746">The philosophy of routing is that nothing is included by default, and that `UseEndpoints` is the place to register endpoints.</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="57132-1747">라우팅 통합 미들웨어 만들기</span><span class="sxs-lookup"><span data-stu-id="57132-1747">Creating routing-integrated middleware</span></span>

<span data-ttu-id="57132-1748">메타데이터 형식을 인터페이스로 정의하는 것이 **좋습니다**.</span><span class="sxs-lookup"><span data-stu-id="57132-1748">**CONSIDER** defining metadata types as an interface.</span></span>

<span data-ttu-id="57132-1749">메타데이터 형식을 클래스 및 메서드의 특성으로 사용할 수 있게 **하세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1749">**DO** make it possible to use metadata types as an attribute on classes and methods.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="57132-1750">컨트롤러 및 Razor Pages와 같은 프레임워크는 형식 및 메서드에 메타데이터 특성을 적용하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1750">Frameworks like controllers and Razor Pages support applying metadata attributes to types and methods.</span></span> <span data-ttu-id="57132-1751">메타데이터 형식을 선언하는 경우:</span><span class="sxs-lookup"><span data-stu-id="57132-1751">If you declare metadata types:</span></span>

* <span data-ttu-id="57132-1752">이 형식에 [특성](/dotnet/csharp/programming-guide/concepts/attributes/)으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1752">Make them accessible as [attributes](/dotnet/csharp/programming-guide/concepts/attributes/).</span></span>
* <span data-ttu-id="57132-1753">사용자 대부분이 특성을 적용하는 데 익숙합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1753">Most users are familiar with applying attributes.</span></span>

<span data-ttu-id="57132-1754">메타데이터 형식을 인터페이스로 선언하면 또 하나의 유연성 계층이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1754">Declaring a metadata type as an interface adds another layer of flexibility:</span></span>

* <span data-ttu-id="57132-1755">인터페이스는 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1755">Interfaces are composable.</span></span>
* <span data-ttu-id="57132-1756">개발자가 여러 정책을 결합하여 고유한 형식을 선언할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1756">Developers can declare their own types that combine multiple policies.</span></span>

<span data-ttu-id="57132-1757">다음 예제와 같이 메타데이터를 재정의할 수 있게 **하세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1757">**DO** make it possible to override metadata, as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="57132-1758">이러한 지침을 따르는 가장 좋은 방법은 **마커 메타데이터**를 정의하지 않는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1758">The best way to follow these guidelines is to avoid defining **marker metadata**:</span></span>

* <span data-ttu-id="57132-1759">메타데이터 형식이 있는지만 확인하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1759">Don't just look for the presence of a metadata type.</span></span>
* <span data-ttu-id="57132-1760">메타데이터의 속성을 정의하고 속성을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1760">Define a property on the metadata and check the property.</span></span>

<span data-ttu-id="57132-1761">메타데이터 컬렉션은 순서 지정되며 우선 순위별로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1761">The metadata collection is ordered and supports overriding by priority.</span></span> <span data-ttu-id="57132-1762">컨트롤러의 경우 작업 메서드의 메타데이터가 가장 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1762">In the case of controllers, metadata on the action method is most specific.</span></span>

<span data-ttu-id="57132-1763">라우팅이 있는지와 관계없이 미들웨어를 유용하게 사용할 수 있게 **하세요**.</span><span class="sxs-lookup"><span data-stu-id="57132-1763">**DO** make middleware useful with and without routing.</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

<span data-ttu-id="57132-1764">이 지침의 예제에서는 `UseAuthorization` 미들웨어를 고려해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1764">As an example of this guideline, consider the `UseAuthorization` middleware.</span></span> <span data-ttu-id="57132-1765">권한 부여 미들웨어를 사용하면 대체 정책을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1765">The authorization middleware allows you to pass in a fallback policy.</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="57132-1766">대체 정책(지정된 경우)은 다음 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1766">The fallback policy, if specified, applies to both:</span></span>

* <span data-ttu-id="57132-1767">지정된 정책이 없는 엔드포인트</span><span class="sxs-lookup"><span data-stu-id="57132-1767">Endpoints without a specified policy.</span></span>
* <span data-ttu-id="57132-1768">엔드포인트와 일치하지 않는 요청</span><span class="sxs-lookup"><span data-stu-id="57132-1768">Requests that don't match an endpoint.</span></span>

<span data-ttu-id="57132-1769">따라서 권한 부여 미들웨어는 라우팅 컨텍스트 외에서도 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1769">This makes the authorization middleware useful outside of the context of routing.</span></span> <span data-ttu-id="57132-1770">권한 부여 미들웨어는 기존 미들웨어 프로그래밍에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1770">The authorization middleware can be used for traditional middleware programming.</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="57132-1771">라우팅은 요청 URI를 엔드포인트에 매핑하고 들어오는 요청을 이러한 엔드포인트로 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1771">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="57132-1772">경로는 앱에서 정의되고 앱 시작 시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1772">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="57132-1773">경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1773">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="57132-1774">또한 라우팅은 앱의 경로 정보를 사용하여 엔드포인트에 매핑되는 URL을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1774">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="57132-1775">ASP.NET Core 2.2에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1775">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="57132-1776"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 옵션은 라우팅에서 내부적으로 엔드포인트 기반 논리를 사용해야 하는지, 또는 ASP.NET Core 2.1 이하의 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 논리를 사용해야 하는지 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1776">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="57132-1777">호환성 버전이 2.2 이상으로 설정된 경우 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1777">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="57132-1778">이전 라우팅 논리를 사용하려면 값을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1778">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="57132-1779"><xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.1 버전](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1779">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57132-1780">이 문서에서는 낮은 수준의 ASP.NET Core 라우팅을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1780">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="57132-1781">ASP.NET Core MVC 라우팅에 대한 내용은 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1781">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="57132-1782">Razor Pages의 라우팅 규칙에 대한 내용은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1782">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="57132-1783">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57132-1783">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="57132-1784">라우팅 기본 사항</span><span class="sxs-lookup"><span data-stu-id="57132-1784">Routing basics</span></span>

<span data-ttu-id="57132-1785">대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1785">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="57132-1786">기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:</span><span class="sxs-lookup"><span data-stu-id="57132-1786">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="57132-1787">기본적이고 서술적인 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1787">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="57132-1788">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1788">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="57132-1789">일반적으로 개발자는 [특성 라우팅](xref:mvc/controllers/routing#attribute-routing) 또는 전용 기존 경로를 사용하여 특수한 상황에서 앱의 트래픽이 높은 영역에 간결한 추가 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1789">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span> <span data-ttu-id="57132-1790">특수한 상황 예제로는 블로그 및 전자 상거래 엔드포인트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1790">Specialized situations examples include, blog and ecommerce endpoints.</span></span>

<span data-ttu-id="57132-1791">Web API는 특성 라우팅을 사용하여 작업이 HTTP 동사로 표현되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1791">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="57132-1792">이는 동일한 논리 리소스의 많은 작업(예: GET, POST)이 동일한 URL을 사용한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1792">This means that many operations, for example, GET, and POST, on the same logical resource use the same URL.</span></span> <span data-ttu-id="57132-1793">특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1793">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="57132-1794"> Pages 앱은 기본 기존 라우팅을 사용하여 앱의 *Pages* 폴더에서 명명된 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1794"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="57132-1795">Razor Pages 라우팅 동작을 사용자 지정할 수 있는 추가 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1795">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="57132-1796">자세한 내용은 <xref:razor-pages/index> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1796">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="57132-1797">URL 생성 지원을 사용하면 URL을 하드 코딩하지 않고 앱을 개발하여 앱을 서로 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1797">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="57132-1798">이 지원을 사용하면 기본 라우팅 구성으로 시작하고 앱의 리소스 레이아웃이 결정된 후에 해당 경로를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1798">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="57132-1799">라우팅은 *엔드포인트*(`Endpoint`)를 사용하여 앱에서 논리적 엔드포인트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1799">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="57132-1800">엔드포인트는 요청을 처리할 대리자와 임의의 메타데이터 컬렉션을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1800">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="57132-1801">메타데이터는 각 엔드포인트에 연결된 정책과 구성에 따라 횡단 관심사(Cross-Cutting Concerns)를 구현하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1801">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="57132-1802">라우팅 시스템에는 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1802">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="57132-1803">경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1803">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="57132-1804">기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1804">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="57132-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="57132-1806">MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 엔드포인트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1806">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="57132-1807">라우팅 구현은 미들웨어 파이프라인에서 필요한 곳이라면 어디서든지 라우팅 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1807">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="57132-1808">라우팅 미들웨어 이후에 나타나는 미들웨어는 지정된 요청 URI에 대한 라우팅 미들웨어의 엔드포인트 결정 결과를 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1808">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="57132-1809">미들웨어 파이프라인의 어느 곳에서든 앱의 모든 엔드포인트를 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1809">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="57132-1810">앱은 라우팅을 사용하여 엔드포인트 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1810">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="57132-1811">URL 생성은 임의의 확장성을 지원하는 주소를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1811">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="57132-1812">링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 URL을 생성하기 위해 어디서나 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1812">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="57132-1813">DI를 통해 링크 생성기 API를 사용할 수 없는 경우 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>에서 URL을 작성하는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1813">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="57132-1814">ASP.NET Core 2.2의 엔드포인트 라우팅이 릴리스되면서 엔드포인트 연결이 MVC/Razor Pages 작업 및 페이지로 제한되었습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1814">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="57132-1815">이후 릴리스에서는 엔드포인트 연결 기능이 확장될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1815">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="57132-1816">라우팅은 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 클래스에 의해 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1816">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="57132-1817">[ASP.NET Core MVC](xref:mvc/overview)는 라우팅을 해당 구성의 일부로 미들웨어 파이프라인에 추가하고, MVC 및 Razor Pages 앱에서 라우팅을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1817">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="57132-1818">라우팅을 독립 실행형 구성 요소로 사용하는 방법을 알아보려면 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-1818">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="57132-1819">URL 일치</span><span class="sxs-lookup"><span data-stu-id="57132-1819">URL matching</span></span>

<span data-ttu-id="57132-1820">URL 일치는 라우팅이 들어오는 요청을 *엔드포인트*로 디스패치하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1820">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="57132-1821">이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1821">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="57132-1822">요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1822">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="57132-1823">엔드포인트 라우팅의 라우팅 시스템은 모든 디스패치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1823">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="57132-1824">미들웨어가 선택된 엔드포인트를 기반으로 정책을 적용하므로 디스패치 또는 애플리케이션의 보안 정책에 영향을 미칠 수 있는 모든 결정은 라우팅 시스템 내에서 이루어져야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1824">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="57132-1825">엔드포인트 대리자가 실행되면 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성이 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1825">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="57132-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*)는 경로에서 생성된 *경로 값*의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="57132-1827">이러한 값은 일반적으로 URL을 토큰화하여 결정되고, 사용자 입력을 수락하거나 앱 내부의 추가 디스패치 결정을 내리는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1827">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="57132-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)는 일치하는 경로와 관련된 추가 데이터의 속성 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="57132-1829"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>는 앱에서 일치된 경로에 따라 결정할 수 있도록 각 경로와 상태 데이터의 연결을 지원하기 위해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1829"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="57132-1830">이러한 값은 개발자 정의되고 어떤 방식으로든 라우팅의 동작에 영향을 주지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="57132-1830">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="57132-1831">또한 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)에 안전하게 배치되는(stashed) 값은 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values)와는 달리 문자열 간에 변환될 수 있어야 하는 모든 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1831">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="57132-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers)는 요청을 성공적으로 일치하는 데 참여한 경로의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="57132-1833">경로는 서로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1833">Routes can be nested inside of one another.</span></span> <span data-ttu-id="57132-1834"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 속성은 일치한 경로의 논리 트리를 통해 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1834">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="57132-1835">일반적으로 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 첫 번째 항목은 경로 컬렉션이며 URL 생성을 위해 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1835">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="57132-1836"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 마지막 항목은 일치한 경로 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1836">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="57132-1837">LinkGenerator를 사용한 URL 생성</span><span class="sxs-lookup"><span data-stu-id="57132-1837">URL generation with LinkGenerator</span></span>

<span data-ttu-id="57132-1838">URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1838">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="57132-1839">이렇게 하면 엔드포인트와 이에 액세스하는 URL을 논리적으로 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1839">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="57132-1840">엔드포인트 라우팅에는 링크 생성기 API(<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1840">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="57132-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 [DI](xref:fundamentals/dependency-injection)에서 가져올 수 있는 싱글톤 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57132-1842">API는 실행 중인 요청의 컨텍스트 외부에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1842">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="57132-1843">MVC의 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 및 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>를 사용하는 시나리오(예: [태그 도우미](xref:mvc/views/tag-helpers/intro), HTML 도우미 및 [작업 결과](xref:mvc/controllers/actions))는 링크 생성기를 사용하여 링크 생성 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1843">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="57132-1844">링크 생성기는 *주소* 및 *주소 체계*의 개념으로 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1844">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="57132-1845">주소 체계는 링크 생성을 위해 고려해야 할 엔드포인트를 결정하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1845">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="57132-1846">예를 들어 많은 사용자가 MVC/Razor Pages에서 친숙한 경로 이름 및 경로 값 시나리오는 주소 체계로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1846">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="57132-1847">링크 생성기는 다음 확장 메서드를 통해 MVC/Razor Pages 작업 및 페이지에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1847">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="57132-1848">이러한 메서드의 오버로드에는 `HttpContext`를 포함한 인수가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1848">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="57132-1849">이러한 메서드는 기능적으로 `Url.Action` 및 `Url.Page`와 동일하지만, 추가적인 유연성과 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1849">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="57132-1850">`GetPath*` 메서드는 절대 경로가 포함된 URI를 생성한다는 점에서 `Url.Action` 및 `Url.Page`와 가장 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1850">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="57132-1851">`GetUri*` 메서드는 항상 체계와 호스트를 포함한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1851">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="57132-1852">`HttpContext`를 허용하는 메서드는 실행 중인 요청의 컨텍스트에서 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1852">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="57132-1853">재정의되지 않는 한 실행 중인 요청의 앰비언트 경로 값, URL 기본 경로, 체계 및 호스트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1853">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="57132-1854"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는 주소를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1854"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="57132-1855">URI 생성은 다음 두 단계로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1855">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="57132-1856">주소는 해당 주소와 일치하는 엔드포인트 목록에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1856">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="57132-1857">제공된 값과 일치하는 경로 패턴을 찾을 때까지 각 엔드포인트의 `RoutePattern`이 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1857">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="57132-1858">결과 출력은 링크 생성기에 제공된 다른 URI 부분과 결합되어 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1858">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="57132-1859"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>에서 제공하는 메서드는 모든 유형의 주소에 대해 표준 링크 생성 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1859">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="57132-1860">링크 생성기를 사용하는 가장 편리한 방법은 특정 주소 유형에 대한 작업을 수행하는 확장 메서드를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-1860">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="57132-1861">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="57132-1861">Extension Method</span></span>   | <span data-ttu-id="57132-1862">설명</span><span class="sxs-lookup"><span data-stu-id="57132-1862">Description</span></span>                                                         |
| ---
<span data-ttu-id="57132-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1864">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1864">'Blazor'</span></span>
- <span data-ttu-id="57132-1865">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1865">'Identity'</span></span>
- <span data-ttu-id="57132-1866">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1866">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1867">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1867">'Razor'</span></span>
- <span data-ttu-id="57132-1868">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1868">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1870">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1870">'Blazor'</span></span>
- <span data-ttu-id="57132-1871">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1871">'Identity'</span></span>
- <span data-ttu-id="57132-1872">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1872">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1873">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1873">'Razor'</span></span>
- <span data-ttu-id="57132-1874">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1874">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1876">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1876">'Blazor'</span></span>
- <span data-ttu-id="57132-1877">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1877">'Identity'</span></span>
- <span data-ttu-id="57132-1878">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1878">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1879">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1879">'Razor'</span></span>
- <span data-ttu-id="57132-1880">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1880">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1882">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1882">'Blazor'</span></span>
- <span data-ttu-id="57132-1883">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1883">'Identity'</span></span>
- <span data-ttu-id="57132-1884">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1884">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1885">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1885">'Razor'</span></span>
- <span data-ttu-id="57132-1886">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1886">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1888">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1888">'Blazor'</span></span>
- <span data-ttu-id="57132-1889">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1889">'Identity'</span></span>
- <span data-ttu-id="57132-1890">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1890">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1891">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1891">'Razor'</span></span>
- <span data-ttu-id="57132-1892">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1892">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1894">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1894">'Blazor'</span></span>
- <span data-ttu-id="57132-1895">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1895">'Identity'</span></span>
- <span data-ttu-id="57132-1896">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1896">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1897">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1897">'Razor'</span></span>
- <span data-ttu-id="57132-1898">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1898">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1900">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1900">'Blazor'</span></span>
- <span data-ttu-id="57132-1901">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1901">'Identity'</span></span>
- <span data-ttu-id="57132-1902">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1902">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1903">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1903">'Razor'</span></span>
- <span data-ttu-id="57132-1904">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1904">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1906">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1906">'Blazor'</span></span>
- <span data-ttu-id="57132-1907">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1907">'Identity'</span></span>
- <span data-ttu-id="57132-1908">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1908">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1909">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1909">'Razor'</span></span>
- <span data-ttu-id="57132-1910">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1910">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1912">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1912">'Blazor'</span></span>
- <span data-ttu-id="57132-1913">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1913">'Identity'</span></span>
- <span data-ttu-id="57132-1914">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1914">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1915">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1915">'Razor'</span></span>
- <span data-ttu-id="57132-1916">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1916">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1918">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1918">'Blazor'</span></span>
- <span data-ttu-id="57132-1919">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1919">'Identity'</span></span>
- <span data-ttu-id="57132-1920">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1920">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1921">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1921">'Razor'</span></span>
- <span data-ttu-id="57132-1922">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1922">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1924">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1924">'Blazor'</span></span>
- <span data-ttu-id="57132-1925">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1925">'Identity'</span></span>
- <span data-ttu-id="57132-1926">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1926">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1927">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1927">'Razor'</span></span>
- <span data-ttu-id="57132-1928">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1928">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1930">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1930">'Blazor'</span></span>
- <span data-ttu-id="57132-1931">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1931">'Identity'</span></span>
- <span data-ttu-id="57132-1932">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1932">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1933">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1933">'Razor'</span></span>
- <span data-ttu-id="57132-1934">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1934">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1936">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1936">'Blazor'</span></span>
- <span data-ttu-id="57132-1937">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1937">'Identity'</span></span>
- <span data-ttu-id="57132-1938">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1938">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1939">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1939">'Razor'</span></span>
- <span data-ttu-id="57132-1940">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1940">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1942">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1942">'Blazor'</span></span>
- <span data-ttu-id="57132-1943">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1943">'Identity'</span></span>
- <span data-ttu-id="57132-1944">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1944">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1945">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1945">'Razor'</span></span>
- <span data-ttu-id="57132-1946">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1946">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1948">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1948">'Blazor'</span></span>
- <span data-ttu-id="57132-1949">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1949">'Identity'</span></span>
- <span data-ttu-id="57132-1950">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1950">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1951">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1951">'Razor'</span></span>
- <span data-ttu-id="57132-1952">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1952">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1954">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1954">'Blazor'</span></span>
- <span data-ttu-id="57132-1955">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1955">'Identity'</span></span>
- <span data-ttu-id="57132-1956">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1956">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1957">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1957">'Razor'</span></span>
- <span data-ttu-id="57132-1958">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1958">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1960">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1960">'Blazor'</span></span>
- <span data-ttu-id="57132-1961">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1961">'Identity'</span></span>
- <span data-ttu-id="57132-1962">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1962">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1963">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1963">'Razor'</span></span>
- <span data-ttu-id="57132-1964">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1964">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1966">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1966">'Blazor'</span></span>
- <span data-ttu-id="57132-1967">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1967">'Identity'</span></span>
- <span data-ttu-id="57132-1968">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1968">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1969">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1969">'Razor'</span></span>
- <span data-ttu-id="57132-1970">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1970">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1972">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1972">'Blazor'</span></span>
- <span data-ttu-id="57132-1973">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1973">'Identity'</span></span>
- <span data-ttu-id="57132-1974">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1974">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1975">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1975">'Razor'</span></span>
- <span data-ttu-id="57132-1976">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1976">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1978">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1978">'Blazor'</span></span>
- <span data-ttu-id="57132-1979">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1979">'Identity'</span></span>
- <span data-ttu-id="57132-1980">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1980">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1981">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1981">'Razor'</span></span>
- <span data-ttu-id="57132-1982">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1982">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1984">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1984">'Blazor'</span></span>
- <span data-ttu-id="57132-1985">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1985">'Identity'</span></span>
- <span data-ttu-id="57132-1986">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1986">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1987">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1987">'Razor'</span></span>
- <span data-ttu-id="57132-1988">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1988">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1990">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1990">'Blazor'</span></span>
- <span data-ttu-id="57132-1991">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1991">'Identity'</span></span>
- <span data-ttu-id="57132-1992">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1992">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1993">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1993">'Razor'</span></span>
- <span data-ttu-id="57132-1994">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-1994">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-1996">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-1996">'Blazor'</span></span>
- <span data-ttu-id="57132-1997">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-1997">'Identity'</span></span>
- <span data-ttu-id="57132-1998">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-1998">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-1999">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-1999">'Razor'</span></span>
- <span data-ttu-id="57132-2000">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2000">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2002">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2002">'Blazor'</span></span>
- <span data-ttu-id="57132-2003">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2003">'Identity'</span></span>
- <span data-ttu-id="57132-2004">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2004">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2005">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2005">'Razor'</span></span>
- <span data-ttu-id="57132-2006">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2006">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2008">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2008">'Blazor'</span></span>
- <span data-ttu-id="57132-2009">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2009">'Identity'</span></span>
- <span data-ttu-id="57132-2010">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2010">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2011">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2011">'Razor'</span></span>
- <span data-ttu-id="57132-2012">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2012">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2014">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2014">'Blazor'</span></span>
- <span data-ttu-id="57132-2015">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2015">'Identity'</span></span>
- <span data-ttu-id="57132-2016">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2016">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2017">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2017">'Razor'</span></span>
- <span data-ttu-id="57132-2018">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2018">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2020">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2020">'Blazor'</span></span>
- <span data-ttu-id="57132-2021">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2021">'Identity'</span></span>
- <span data-ttu-id="57132-2022">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2022">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2023">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2023">'Razor'</span></span>
- <span data-ttu-id="57132-2024">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2024">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2026">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2026">'Blazor'</span></span>
- <span data-ttu-id="57132-2027">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2027">'Identity'</span></span>
- <span data-ttu-id="57132-2028">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2028">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2029">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2029">'Razor'</span></span>
- <span data-ttu-id="57132-2030">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2030">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2032">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2032">'Blazor'</span></span>
- <span data-ttu-id="57132-2033">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2033">'Identity'</span></span>
- <span data-ttu-id="57132-2034">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2034">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2035">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2035">'Razor'</span></span>
- <span data-ttu-id="57132-2036">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2036">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2038">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2038">'Blazor'</span></span>
- <span data-ttu-id="57132-2039">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2039">'Identity'</span></span>
- <span data-ttu-id="57132-2040">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2040">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2041">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2041">'Razor'</span></span>
- <span data-ttu-id="57132-2042">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2042">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2044">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2044">'Blazor'</span></span>
- <span data-ttu-id="57132-2045">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2045">'Identity'</span></span>
- <span data-ttu-id="57132-2046">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2046">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2047">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2047">'Razor'</span></span>
- <span data-ttu-id="57132-2048">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2048">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2050">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2050">'Blazor'</span></span>
- <span data-ttu-id="57132-2051">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2051">'Identity'</span></span>
- <span data-ttu-id="57132-2052">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2052">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2053">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2053">'Razor'</span></span>
- <span data-ttu-id="57132-2054">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2054">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2056">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2056">'Blazor'</span></span>
- <span data-ttu-id="57132-2057">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2057">'Identity'</span></span>
- <span data-ttu-id="57132-2058">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2058">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2059">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2059">'Razor'</span></span>
- <span data-ttu-id="57132-2060">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2060">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2062">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2062">'Blazor'</span></span>
- <span data-ttu-id="57132-2063">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2063">'Identity'</span></span>
- <span data-ttu-id="57132-2064">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2064">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2065">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2065">'Razor'</span></span>
- <span data-ttu-id="57132-2066">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2066">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2068">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2068">'Blazor'</span></span>
- <span data-ttu-id="57132-2069">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2069">'Identity'</span></span>
- <span data-ttu-id="57132-2070">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2070">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2071">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2071">'Razor'</span></span>
- <span data-ttu-id="57132-2072">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2072">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2074">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2074">'Blazor'</span></span>
- <span data-ttu-id="57132-2075">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2075">'Identity'</span></span>
- <span data-ttu-id="57132-2076">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2076">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2077">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2077">'Razor'</span></span>
- <span data-ttu-id="57132-2078">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2078">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2080">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2080">'Blazor'</span></span>
- <span data-ttu-id="57132-2081">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2081">'Identity'</span></span>
- <span data-ttu-id="57132-2082">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2082">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2083">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2083">'Razor'</span></span>
- <span data-ttu-id="57132-2084">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2084">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2086">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2086">'Blazor'</span></span>
- <span data-ttu-id="57132-2087">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2087">'Identity'</span></span>
- <span data-ttu-id="57132-2088">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2088">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2089">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2089">'Razor'</span></span>
- <span data-ttu-id="57132-2090">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2090">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 제공된 값에 기반한 절대 경로의 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="57132-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 제공된 값에 기반한 절대 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="57132-2093"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 메서드 호출 시 다음과 같은 의미에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2093">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="57132-2094">들어오는 요청의 `GetUri*` 헤더의 유효성을 검사하지 않는 앱 구성에서는 `Host` 확장 메서드를 신중하게 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2094">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="57132-2095">들어오는 요청의 `Host` 헤더의 유효성을 검사하지 않으면 신뢰할 수 없는 요청 입력이 보기/페이지에 포함된 URI로 클라이언트에 다시 보내질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2095">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="57132-2096">모든 프로덕션 앱은 알려진 유효한 값에 대해 `Host` 헤더의 유효성을 검사하도록 서버를 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2096">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="57132-2097">미들웨어에서 `MapWhen` 또는 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>과 함께 `Map`를 사용할 때는 신중하게 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2097">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="57132-2098">`Map*`는 실행 중인 요청의 기본 경로를 변경하여 링크 생성의 출력에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2098">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="57132-2099">모든 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API는 기본 경로를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2099">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="57132-2100">링크 생성에 대한 `Map*`의 영향을 취소하려면 항상 빈 기본 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2100">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="57132-2101">이전 버전의 라우팅과의 차이점</span><span class="sxs-lookup"><span data-stu-id="57132-2101">Differences from earlier versions of routing</span></span>

<span data-ttu-id="57132-2102">ASP.NET Core 2.2 이상의 엔드포인트 라우팅과 ASP.NET Core 이전 버전의 라우팅 간에는 다음과 같은 몇 가지 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2102">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="57132-2103">엔드포인트 라우팅 시스템은 <xref:Microsoft.AspNetCore.Routing.Route>에서 상속하는 것을 포함하여 <xref:Microsoft.AspNetCore.Routing.IRouter> 기반 확장성을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2103">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="57132-2104">엔드포인트 라우팅은 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2104">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="57132-2105">호환성 shim을 계속 사용하려면 2.1 [호환성 버전](xref:mvc/compatibility-version)(`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`)을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2105">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="57132-2106">엔드포인트 라우팅은 기존 경로를 사용하여 생성된 URI의 대/소문자 표기와 다른 동작을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2106">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="57132-2107">다음과 같은 기본 경로 템플릿을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2107">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="57132-2108">다음 경로를 사용하여 작업에 대한 링크를 생성한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2108">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="57132-2109"><xref:Microsoft.AspNetCore.Routing.IRouter> 기반 라우팅을 사용하는 경우 이 코드는 제공된 경로 값의 대/소문자 표기를 고려하여 `/blog/ReadPost/17`이라는 URI를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2109">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="57132-2110">ASP.NET Core 2.2 이상의 엔드포인트 라우팅에서는 `/Blog/ReadPost/17`을 생성합니다("Blog"의 첫 글자가 대문자로 지정됨).</span><span class="sxs-lookup"><span data-stu-id="57132-2110">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="57132-2111">엔드포인트 라우팅은 이 동작을 전역으로 사용자 지정하거나 URL 매핑에 대해 다른 규칙을 적용하는 데 사용할 수 있는 `IOutboundParameterTransformer` 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2111">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="57132-2112">자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2112">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="57132-2113">MVC/Razor Pages에서 기존 경로를 통해 사용하는 링크 생성이 존재하지 않는 컨트롤러/작업 또는 페이지에 연결하려고 할 때 다르게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2113">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="57132-2114">다음과 같은 기본 경로 템플릿을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2114">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="57132-2115">다음과 함께 기본 템플릿을 사용하여 작업에 대한 링크를 생성한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2115">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="57132-2116">`IRouter` 기반 라우팅을 사용하면 `BlogController`가 존재하지 않거나 `ReadPost` 작업 메서드가 존재하지 않는 경우에도 결과는 항상 `/Blog/ReadPost/17`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2116">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="57132-2117">예상대로, 작업 메서드가 존재할 경우 ASP.NET Core 2.2 이상의 엔드포인트 라우팅은 `/Blog/ReadPost/17`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2117">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="57132-2118">*그러나 작업이 존재하지 않을 경우 엔드포인트 라우팅은 빈 문자열을 생성합니다.*</span><span class="sxs-lookup"><span data-stu-id="57132-2118">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="57132-2119">개념적으로 엔드포인트 라우팅은 작업이 존재하지 않는 경우 엔드포인트가 존재한다고 가정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2119">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="57132-2120">링크 생성 *앰비언트 값 무효화 알고리즘*은 엔드포인트 라우팅에서 사용할 때 다르게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2120">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="57132-2121">*앰비언트 값 무효화*는 링크 생성 작업에서 사용할 수 있는 현재 실행 중인 요청의 경로 값(앰비언트 값)을 결정하는 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2121">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="57132-2122">기존 라우팅은 다른 작업에 연결할 때 항상 추가 경로 값을 무효화했습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2122">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="57132-2123">ASP.NET Core 2.2를 릴리스하기 전에는 특성 라우팅에 이 동작이 없었습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2123">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="57132-2124">이전 버전의 ASP.NET Core에서는 동일한 경로 매개 변수 이름을 사용하는 다른 작업에 연결하면 링크 생성 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2124">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="57132-2125">ASP.NET Core 2.2 이상에서는 두 가지 형태의 라우팅 모두에서 다른 작업에 연결하면 값이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2125">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="57132-2126">ASP.NET Core 2.1 이하에서 다음 예제를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2126">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="57132-2127">다른 작업(또는 다른 페이지)에 연결할 때 경로 값을 원하지 않는 방법으로 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2127">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="57132-2128">*/Pages/Store/Product.cshtml*,</span><span class="sxs-lookup"><span data-stu-id="57132-2128">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="57132-2129">*/Pages/Login.cshtml*,</span><span class="sxs-lookup"><span data-stu-id="57132-2129">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="57132-2130">ASP.NET Core 2.1 이하에서 URI가 `/Store/Product/18`인 경우 Store/Info 페이지에서 `@Url.Page("/Login")`로 생성된 링크는 `/Login/18`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2130">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="57132-2131">링크 대상이 완전히 다른 앱 부분인 경우에도 18이라는 `id` 값이 다시 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2131">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="57132-2132">`/Login` 페이지의 컨텍스트에 있는 `id` 경로 값은 상점 제품 ID 값이 아닌 사용자 ID 값일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2132">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="57132-2133">ASP.NET Core 2.2 이상을 사용하는 엔드포인트 라우팅에서 결과는 `/Login`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2133">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="57132-2134">연결된 대상이 다른 작업 또는 페이지인 경우 앰비언트 값은 다시 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2134">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="57132-2135">라운드트립 경로 매개 변수 구문: 이중 별표(`**`) 범용(catch-all) 매개 변수 구문을 사용하는 경우 슬래시는 인코딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2135">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="57132-2136">링크를 생성하는 동안 라우팅 시스템은 슬래시를 제외한 이중 별표(`**`) 범용 매개 변수(예: `{**myparametername}`)에서 캡처된 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2136">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="57132-2137">이중 별표 범용 매개 변수는 ASP.NET Core 2.2 이상의 `IRouter` 기반 라우팅에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2137">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="57132-2138">ASP.NET Core 이전 버전의 단일 별표 범용 매개 변수 구문(`{*myparametername}`)은 계속 지원되며, 슬래시가 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2138">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="57132-2139">경로</span><span class="sxs-lookup"><span data-stu-id="57132-2139">Route</span></span>              | <span data-ttu-id="57132-2140">다음을 사용하여 생성되는 링크</span><span class="sxs-lookup"><span data-stu-id="57132-2140">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ---
<span data-ttu-id="57132-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2142">'Blazor'</span></span>
- <span data-ttu-id="57132-2143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2143">'Identity'</span></span>
- <span data-ttu-id="57132-2144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2144">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2145">'Razor'</span></span>
- <span data-ttu-id="57132-2146">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2148">'Blazor'</span></span>
- <span data-ttu-id="57132-2149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2149">'Identity'</span></span>
- <span data-ttu-id="57132-2150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2150">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2151">'Razor'</span></span>
- <span data-ttu-id="57132-2152">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2154">'Blazor'</span></span>
- <span data-ttu-id="57132-2155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2155">'Identity'</span></span>
- <span data-ttu-id="57132-2156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2156">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2157">'Razor'</span></span>
- <span data-ttu-id="57132-2158">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2160">'Blazor'</span></span>
- <span data-ttu-id="57132-2161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2161">'Identity'</span></span>
- <span data-ttu-id="57132-2162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2162">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2163">'Razor'</span></span>
- <span data-ttu-id="57132-2164">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2166">'Blazor'</span></span>
- <span data-ttu-id="57132-2167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2167">'Identity'</span></span>
- <span data-ttu-id="57132-2168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2168">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2169">'Razor'</span></span>
- <span data-ttu-id="57132-2170">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2172">'Blazor'</span></span>
- <span data-ttu-id="57132-2173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2173">'Identity'</span></span>
- <span data-ttu-id="57132-2174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2174">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2175">'Razor'</span></span>
- <span data-ttu-id="57132-2176">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2178">'Blazor'</span></span>
- <span data-ttu-id="57132-2179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2179">'Identity'</span></span>
- <span data-ttu-id="57132-2180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2180">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2181">'Razor'</span></span>
- <span data-ttu-id="57132-2182">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2182">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2184">'Blazor'</span></span>
- <span data-ttu-id="57132-2185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2185">'Identity'</span></span>
- <span data-ttu-id="57132-2186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2186">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2187">'Razor'</span></span>
- <span data-ttu-id="57132-2188">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2190">'Blazor'</span></span>
- <span data-ttu-id="57132-2191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2191">'Identity'</span></span>
- <span data-ttu-id="57132-2192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2192">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2193">'Razor'</span></span>
- <span data-ttu-id="57132-2194">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2196">'Blazor'</span></span>
- <span data-ttu-id="57132-2197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2197">'Identity'</span></span>
- <span data-ttu-id="57132-2198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2198">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2199">'Razor'</span></span>
- <span data-ttu-id="57132-2200">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2202">'Blazor'</span></span>
- <span data-ttu-id="57132-2203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2203">'Identity'</span></span>
- <span data-ttu-id="57132-2204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2204">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2205">'Razor'</span></span>
- <span data-ttu-id="57132-2206">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2208">'Blazor'</span></span>
- <span data-ttu-id="57132-2209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2209">'Identity'</span></span>
- <span data-ttu-id="57132-2210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2210">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2211">'Razor'</span></span>
- <span data-ttu-id="57132-2212">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2214">'Blazor'</span></span>
- <span data-ttu-id="57132-2215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2215">'Identity'</span></span>
- <span data-ttu-id="57132-2216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2216">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2217">'Razor'</span></span>
- <span data-ttu-id="57132-2218">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2220">'Blazor'</span></span>
- <span data-ttu-id="57132-2221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2221">'Identity'</span></span>
- <span data-ttu-id="57132-2222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2222">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2223">'Razor'</span></span>
- <span data-ttu-id="57132-2224">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2226">'Blazor'</span></span>
- <span data-ttu-id="57132-2227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2227">'Identity'</span></span>
- <span data-ttu-id="57132-2228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2228">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2229">'Razor'</span></span>
- <span data-ttu-id="57132-2230">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2232">'Blazor'</span></span>
- <span data-ttu-id="57132-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2233">'Identity'</span></span>
- <span data-ttu-id="57132-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2235">'Razor'</span></span>
- <span data-ttu-id="57132-2236">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2238">'Blazor'</span></span>
- <span data-ttu-id="57132-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2239">'Identity'</span></span>
- <span data-ttu-id="57132-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2241">'Razor'</span></span>
- <span data-ttu-id="57132-2242">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2244">'Blazor'</span></span>
- <span data-ttu-id="57132-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2245">'Identity'</span></span>
- <span data-ttu-id="57132-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2247">'Razor'</span></span>
- <span data-ttu-id="57132-2248">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2250">'Blazor'</span></span>
- <span data-ttu-id="57132-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2251">'Identity'</span></span>
- <span data-ttu-id="57132-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2253">'Razor'</span></span>
- <span data-ttu-id="57132-2254">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2256">'Blazor'</span></span>
- <span data-ttu-id="57132-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2257">'Identity'</span></span>
- <span data-ttu-id="57132-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2259">'Razor'</span></span>
- <span data-ttu-id="57132-2260">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2262">'Blazor'</span></span>
- <span data-ttu-id="57132-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2263">'Identity'</span></span>
- <span data-ttu-id="57132-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2265">'Razor'</span></span>
- <span data-ttu-id="57132-2266">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2268">'Blazor'</span></span>
- <span data-ttu-id="57132-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2269">'Identity'</span></span>
- <span data-ttu-id="57132-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2271">'Razor'</span></span>
- <span data-ttu-id="57132-2272">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2274">'Blazor'</span></span>
- <span data-ttu-id="57132-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2275">'Identity'</span></span>
- <span data-ttu-id="57132-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2277">'Razor'</span></span>
- <span data-ttu-id="57132-2278">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2280">'Blazor'</span></span>
- <span data-ttu-id="57132-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2281">'Identity'</span></span>
- <span data-ttu-id="57132-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2283">'Razor'</span></span>
- <span data-ttu-id="57132-2284">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2286">'Blazor'</span></span>
- <span data-ttu-id="57132-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2287">'Identity'</span></span>
- <span data-ttu-id="57132-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2289">'Razor'</span></span>
- <span data-ttu-id="57132-2290">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2292">'Blazor'</span></span>
- <span data-ttu-id="57132-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2293">'Identity'</span></span>
- <span data-ttu-id="57132-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2295">'Razor'</span></span>
- <span data-ttu-id="57132-2296">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2298">'Blazor'</span></span>
- <span data-ttu-id="57132-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2299">'Identity'</span></span>
- <span data-ttu-id="57132-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2301">'Razor'</span></span>
- <span data-ttu-id="57132-2302">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2304">'Blazor'</span></span>
- <span data-ttu-id="57132-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2305">'Identity'</span></span>
- <span data-ttu-id="57132-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2307">'Razor'</span></span>
- <span data-ttu-id="57132-2308">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2310">'Blazor'</span></span>
- <span data-ttu-id="57132-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2311">'Identity'</span></span>
- <span data-ttu-id="57132-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2313">'Razor'</span></span>
- <span data-ttu-id="57132-2314">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2316">'Blazor'</span></span>
- <span data-ttu-id="57132-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2317">'Identity'</span></span>
- <span data-ttu-id="57132-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2319">'Razor'</span></span>
- <span data-ttu-id="57132-2320">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2322">'Blazor'</span></span>
- <span data-ttu-id="57132-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2323">'Identity'</span></span>
- <span data-ttu-id="57132-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2325">'Razor'</span></span>
- <span data-ttu-id="57132-2326">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2328">'Blazor'</span></span>
- <span data-ttu-id="57132-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2329">'Identity'</span></span>
- <span data-ttu-id="57132-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2331">'Razor'</span></span>
- <span data-ttu-id="57132-2332">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2334">'Blazor'</span></span>
- <span data-ttu-id="57132-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2335">'Identity'</span></span>
- <span data-ttu-id="57132-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2337">'Razor'</span></span>
- <span data-ttu-id="57132-2338">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2340">'Blazor'</span></span>
- <span data-ttu-id="57132-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2341">'Identity'</span></span>
- <span data-ttu-id="57132-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2343">'Razor'</span></span>
- <span data-ttu-id="57132-2344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2346">'Blazor'</span></span>
- <span data-ttu-id="57132-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2347">'Identity'</span></span>
- <span data-ttu-id="57132-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2349">'Razor'</span></span>
- <span data-ttu-id="57132-2350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2352">'Blazor'</span></span>
- <span data-ttu-id="57132-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2353">'Identity'</span></span>
- <span data-ttu-id="57132-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2355">'Razor'</span></span>
- <span data-ttu-id="57132-2356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2358">'Blazor'</span></span>
- <span data-ttu-id="57132-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2359">'Identity'</span></span>
- <span data-ttu-id="57132-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2361">'Razor'</span></span>
- <span data-ttu-id="57132-2362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2364">'Blazor'</span></span>
- <span data-ttu-id="57132-2365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2365">'Identity'</span></span>
- <span data-ttu-id="57132-2366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2366">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2367">'Razor'</span></span>
- <span data-ttu-id="57132-2368">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2370">'Blazor'</span></span>
- <span data-ttu-id="57132-2371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2371">'Identity'</span></span>
- <span data-ttu-id="57132-2372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2372">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2373">'Razor'</span></span>
- <span data-ttu-id="57132-2374">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2374">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts`(슬래시가 인코딩됨)             |   | `/search/{**page}` | `/search/admin/products`                                              |</span><span class="sxs-lookup"><span data-stu-id="57132-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts` (the forward slash is encoded)             |   | `/search/{**page}` | `/search/admin/products`                                              |</span></span>

### <a name="middleware-example"></a><span data-ttu-id="57132-2376">미들웨어 예제</span><span class="sxs-lookup"><span data-stu-id="57132-2376">Middleware example</span></span>

<span data-ttu-id="57132-2377">다음 예제에서는 미들웨어에서 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API를 사용하여 상점 제품을 나열하는 작업 메서드에 대한 링크를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2377">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="57132-2378">링크 생성기를 클래스에 주입하고 `GenerateLink`를 호출하여 앱의 모든 클래스에서 해당 링크 생성기를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2378">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a><span data-ttu-id="57132-2379">경로 만들기</span><span class="sxs-lookup"><span data-stu-id="57132-2379">Create routes</span></span>

<span data-ttu-id="57132-2380">대부분의 앱은 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 또는 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>에 정의된 유사한 확장 메서드 중 하나를 호출하여 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2380">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="57132-2381">모든 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 확장 메서드는 <xref:Microsoft.AspNetCore.Routing.Route>의 인스턴스를 만들고, 경로 컬렉션에 이를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2381">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="57132-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="57132-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="57132-2384">MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2384">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="57132-2385">다음 코드 예제는 일반적인 ASP.NET Core MVC 경로 정의에서 사용되는 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 호출의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2385">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="57132-2386">이 템플릿은 URL 경로와 일치시키고 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2386">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="57132-2387">예를 들어 `/Products/Details/17` 경로는 `{ controller = Products, action = Details, id = 17 }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2387">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="57132-2388">경로 값은 URL 경로를 세그먼트로 분할하고 각 세그먼트를 경로 템플릿의 *경로 매개 변수* 이름과 일치시켜 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2388">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="57132-2389">경로 매개 변수는 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2389">Route parameters are named.</span></span> <span data-ttu-id="57132-2390">매개 변수는 매개 변수 이름을 `{ ... }` 중괄호로 묶어 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2390">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="57132-2391">또한 위의 템플릿은 `/` URL 경로와 일치시키고 `{ controller = Home, action = Index }` 값을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2391">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="57132-2392">이는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 있으며 `id` 경로 매개 변수는 선택 사항이기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2392">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="57132-2393">경로 매개 변수 이름 뒤에 있는 값이 뒤따르는 등호(`=`)는 매개 변수에 대한 기본값을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2393">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="57132-2394">경로 매개 변수 이름 뒤에 있는 물음표(`?`)는 선택적 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2394">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="57132-2395">기본 값을 사용하는 경로 매개 변수는 경로가 일치하는 경우 *항상* 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2395">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="57132-2396">해당 URL 경로 세그먼트가 없는 경우 선택적 매개 변수는 경로 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2396">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="57132-2397">경로 템플릿 시나리오 및 구문에 대한 자세한 설명은 [경로 템플릿 참조](#route-template-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2397">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="57132-2398">다음 예제에서 `{id:int}` 경로 매개 변수 정의는 `id` 경로 매개 변수에 대한 [경로 제약 조건](#route-constraint-reference)을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2398">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="57132-2399">이 템플릿은 `/Products/Details/Apples`가 아닌 `/Products/Details/17`과 같은 URL 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2399">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="57132-2400">경로 제약 조건은 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 구현하고 경로 값을 검사하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2400">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="57132-2401">이 예제에서 경로 값 `id`는 정수로 변환할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2401">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="57132-2402">프레임워크에서 제공하는 경로 제약 조건에 대한 설명은 [경로 제약 조건 참조](#route-constraint-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2402">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="57132-2403"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>의 추가 오버로드는 `constraints`, `dataTokens` 및 `defaults`에 대한 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2403">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="57132-2404">이러한 매개 변수의 일반적인 사용법은 익명 형식의 속성 이름이 경로 매개 변수 이름과 일치하는 익명 형식의 개체를 전달하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2404">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="57132-2405">다음 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 예제에서는 동등한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2405">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="57132-2406">제약 조건 및 기본값을 정의하기 위한 인라인 구문은 단순 경로에 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2406">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="57132-2407">그러나 데이터 토큰과 같이 인라인 구문에서는 지원되지 않는 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2407">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="57132-2408">다음 예제에서는 몇 가지 추가 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2408">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="57132-2409">위의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치하고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2409">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="57132-2410">`controller` 및 `action`에 대한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2410">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="57132-2411">기본값은 경로 템플릿에서 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2411">Default values can be specified in the route template.</span></span> <span data-ttu-id="57132-2412">`article` 경로 매개 변수는 경로 매개 변수 이름 앞에 이중 별표(`**`)를 표시하여 *범용*으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2412">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="57132-2413">범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과도 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2413">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="57132-2414">다음 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2414">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="57132-2415">앞의 템플릿은 `/en-US/Products/5`와 같은 URL 경로와 일치하고, `{ controller = Products, action = Details, id = 5 }` 값 및 `{ locale = en-US }` 데이터 토큰을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2415">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![지역 Windows 토큰](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="57132-2417">Route 클래스 URL 생성</span><span class="sxs-lookup"><span data-stu-id="57132-2417">Route class URL generation</span></span>

<span data-ttu-id="57132-2418"><xref:Microsoft.AspNetCore.Routing.Route> 클래스는 경로 값의 집합을 해당 경로 템플릿과 결합하여 URL 생성을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2418">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="57132-2419">이는 논리적으로 URL 경로와 일치시키는 역방향 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2419">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="57132-2420">URL 생성을 보다 잘 이해하려면 생성하려는 URL을 가정한 다음, 경로 템플릿을 해당 URL과 일치시키는 방법을 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2420">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="57132-2421">어떤 값이 생성되나요?</span><span class="sxs-lookup"><span data-stu-id="57132-2421">What values would be produced?</span></span> <span data-ttu-id="57132-2422">이는 URL 생성이 <xref:Microsoft.AspNetCore.Routing.Route> 클래스에서 작동하는 방식과 대략적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2422">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="57132-2423">다음 예제에서는 일반적인 ASP.NET Core MVC 기본 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2423">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="57132-2424">`{ controller = Products, action = List }` 경로 값을 사용하면 `/Products/List` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2424">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="57132-2425">경로 값은 URL 경로를 구성하기 위해 해당 경로 매개 변수에 대해 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2425">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="57132-2426">`id`는 선택적 경로 매개 변수이므로 `id`에 대한 값 없이 URL이 성공적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2426">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="57132-2427">`{ controller = Home, action = Index }` 경로 값을 사용하면 `/` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2427">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="57132-2428">제공된 경로 값이 기본값과 일치하고, 기본값에 해당하는 세그먼트는 안전하게 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2428">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="57132-2429">뒤이어 언급된 경로 정의(`/Home/Index` 및 `/`)를 사용하는 URL 생성 왕복 모두에서는 URL을 생성하기 위해 사용된 것과 동일한 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2429">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="57132-2430">ASP.NET Core MVC를 사용하는 앱은 라우팅을 직접 호출하는 대신 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper>를 사용하여 URL을 생성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2430">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="57132-2431">URL 생성에 대한 자세한 내용은 [URL 생성 참조](#url-generation-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2431">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="57132-2432">라우팅 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="57132-2432">Use Routing Middleware</span></span>

<span data-ttu-id="57132-2433">앱의 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2433">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="57132-2434">`Startup.ConfigureServices`의 서비스 컨테이너에 라우팅을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2434">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="57132-2435">경로는 `Startup.Configure` 메서드에서 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2435">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="57132-2436">샘플 앱에서 사용하는 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2436">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="57132-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: HTTP GET 요청만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="57132-2438">다음 표는 지정된 URI에 대한 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2438">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="57132-2439">URI</span><span class="sxs-lookup"><span data-stu-id="57132-2439">URI</span></span>                    | <span data-ttu-id="57132-2440">응답</span><span class="sxs-lookup"><span data-stu-id="57132-2440">Response</span></span>                                          |
| ---
<span data-ttu-id="57132-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2442">'Blazor'</span></span>
- <span data-ttu-id="57132-2443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2443">'Identity'</span></span>
- <span data-ttu-id="57132-2444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2444">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2445">'Razor'</span></span>
- <span data-ttu-id="57132-2446">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2448">'Blazor'</span></span>
- <span data-ttu-id="57132-2449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2449">'Identity'</span></span>
- <span data-ttu-id="57132-2450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2450">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2451">'Razor'</span></span>
- <span data-ttu-id="57132-2452">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2454">'Blazor'</span></span>
- <span data-ttu-id="57132-2455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2455">'Identity'</span></span>
- <span data-ttu-id="57132-2456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2456">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2457">'Razor'</span></span>
- <span data-ttu-id="57132-2458">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2460">'Blazor'</span></span>
- <span data-ttu-id="57132-2461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2461">'Identity'</span></span>
- <span data-ttu-id="57132-2462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2462">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2463">'Razor'</span></span>
- <span data-ttu-id="57132-2464">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2466">'Blazor'</span></span>
- <span data-ttu-id="57132-2467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2467">'Identity'</span></span>
- <span data-ttu-id="57132-2468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2468">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2469">'Razor'</span></span>
- <span data-ttu-id="57132-2470">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2472">'Blazor'</span></span>
- <span data-ttu-id="57132-2473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2473">'Identity'</span></span>
- <span data-ttu-id="57132-2474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2474">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2475">'Razor'</span></span>
- <span data-ttu-id="57132-2476">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2478">'Blazor'</span></span>
- <span data-ttu-id="57132-2479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2479">'Identity'</span></span>
- <span data-ttu-id="57132-2480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2480">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2481">'Razor'</span></span>
- <span data-ttu-id="57132-2482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2484">'Blazor'</span></span>
- <span data-ttu-id="57132-2485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2485">'Identity'</span></span>
- <span data-ttu-id="57132-2486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2486">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2487">'Razor'</span></span>
- <span data-ttu-id="57132-2488">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2490">'Blazor'</span></span>
- <span data-ttu-id="57132-2491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2491">'Identity'</span></span>
- <span data-ttu-id="57132-2492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2492">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2493">'Razor'</span></span>
- <span data-ttu-id="57132-2494">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2494">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2496">'Blazor'</span></span>
- <span data-ttu-id="57132-2497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2497">'Identity'</span></span>
- <span data-ttu-id="57132-2498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2498">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2499">'Razor'</span></span>
- <span data-ttu-id="57132-2500">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2502">'Blazor'</span></span>
- <span data-ttu-id="57132-2503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2503">'Identity'</span></span>
- <span data-ttu-id="57132-2504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2504">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2505">'Razor'</span></span>
- <span data-ttu-id="57132-2506">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2508">'Blazor'</span></span>
- <span data-ttu-id="57132-2509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2509">'Identity'</span></span>
- <span data-ttu-id="57132-2510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2510">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2511">'Razor'</span></span>
- <span data-ttu-id="57132-2512">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2514">'Blazor'</span></span>
- <span data-ttu-id="57132-2515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2515">'Identity'</span></span>
- <span data-ttu-id="57132-2516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2516">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2517">'Razor'</span></span>
- <span data-ttu-id="57132-2518">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2520">'Blazor'</span></span>
- <span data-ttu-id="57132-2521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2521">'Identity'</span></span>
- <span data-ttu-id="57132-2522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2522">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2523">'Razor'</span></span>
- <span data-ttu-id="57132-2524">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2526">'Blazor'</span></span>
- <span data-ttu-id="57132-2527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2527">'Identity'</span></span>
- <span data-ttu-id="57132-2528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2528">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2529">'Razor'</span></span>
- <span data-ttu-id="57132-2530">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2532">'Blazor'</span></span>
- <span data-ttu-id="57132-2533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2533">'Identity'</span></span>
- <span data-ttu-id="57132-2534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2534">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2535">'Razor'</span></span>
- <span data-ttu-id="57132-2536">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2538">'Blazor'</span></span>
- <span data-ttu-id="57132-2539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2539">'Identity'</span></span>
- <span data-ttu-id="57132-2540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2540">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2541">'Razor'</span></span>
- <span data-ttu-id="57132-2542">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2544">'Blazor'</span></span>
- <span data-ttu-id="57132-2545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2545">'Identity'</span></span>
- <span data-ttu-id="57132-2546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2546">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2547">'Razor'</span></span>
- <span data-ttu-id="57132-2548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2550">'Blazor'</span></span>
- <span data-ttu-id="57132-2551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2551">'Identity'</span></span>
- <span data-ttu-id="57132-2552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2552">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2553">'Razor'</span></span>
- <span data-ttu-id="57132-2554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2556">'Blazor'</span></span>
- <span data-ttu-id="57132-2557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2557">'Identity'</span></span>
- <span data-ttu-id="57132-2558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2558">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2559">'Razor'</span></span>
- <span data-ttu-id="57132-2560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2562">'Blazor'</span></span>
- <span data-ttu-id="57132-2563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2563">'Identity'</span></span>
- <span data-ttu-id="57132-2564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2564">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2565">'Razor'</span></span>
- <span data-ttu-id="57132-2566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2568">'Blazor'</span></span>
- <span data-ttu-id="57132-2569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2569">'Identity'</span></span>
- <span data-ttu-id="57132-2570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2570">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2571">'Razor'</span></span>
- <span data-ttu-id="57132-2572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2574">'Blazor'</span></span>
- <span data-ttu-id="57132-2575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2575">'Identity'</span></span>
- <span data-ttu-id="57132-2576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2576">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2577">'Razor'</span></span>
- <span data-ttu-id="57132-2578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2580">'Blazor'</span></span>
- <span data-ttu-id="57132-2581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2581">'Identity'</span></span>
- <span data-ttu-id="57132-2582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2582">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2583">'Razor'</span></span>
- <span data-ttu-id="57132-2584">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2586">'Blazor'</span></span>
- <span data-ttu-id="57132-2587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2587">'Identity'</span></span>
- <span data-ttu-id="57132-2588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2588">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2589">'Razor'</span></span>
- <span data-ttu-id="57132-2590">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2592">'Blazor'</span></span>
- <span data-ttu-id="57132-2593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2593">'Identity'</span></span>
- <span data-ttu-id="57132-2594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2594">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2595">'Razor'</span></span>
- <span data-ttu-id="57132-2596">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2598">'Blazor'</span></span>
- <span data-ttu-id="57132-2599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2599">'Identity'</span></span>
- <span data-ttu-id="57132-2600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2600">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2601">'Razor'</span></span>
- <span data-ttu-id="57132-2602">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2604">'Blazor'</span></span>
- <span data-ttu-id="57132-2605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2605">'Identity'</span></span>
- <span data-ttu-id="57132-2606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2606">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2607">'Razor'</span></span>
- <span data-ttu-id="57132-2608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2610">'Blazor'</span></span>
- <span data-ttu-id="57132-2611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2611">'Identity'</span></span>
- <span data-ttu-id="57132-2612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2612">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2613">'Razor'</span></span>
- <span data-ttu-id="57132-2614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2616">'Blazor'</span></span>
- <span data-ttu-id="57132-2617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2617">'Identity'</span></span>
- <span data-ttu-id="57132-2618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2618">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2619">'Razor'</span></span>
- <span data-ttu-id="57132-2620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2622">'Blazor'</span></span>
- <span data-ttu-id="57132-2623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2623">'Identity'</span></span>
- <span data-ttu-id="57132-2624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2624">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2625">'Razor'</span></span>
- <span data-ttu-id="57132-2626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2626">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2627">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-2627">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="57132-2628">경로 값: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-2628">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="57132-2629">경로 값: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-2629">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="57132-2630">경로 값: [operation, track], [id, -3] | | `/package/track/`      | 일치하는 경로가 없으므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2630">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="57132-2631">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="57132-2631">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="57132-2632">| | `POST /hello/Joe`      | HTTP GET만 일치하므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2632">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="57132-2633">| | `GET /hello/Joe/Smith` | 일치하는 경로가 없으므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2633">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="57132-2634">프레임워크는 경로를 만드는 확장 메서드 모음(<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2634">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="57132-2635">`Map[Verb]` 메서드는 제약 조건을 사용하여 메서드 이름에 지정된 HTTP 동사에 대한 경로로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2635">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="57132-2636">예제는 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 및 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2636">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="57132-2637">경로 템플릿 참조</span><span class="sxs-lookup"><span data-stu-id="57132-2637">Route template reference</span></span>

<span data-ttu-id="57132-2638">중괄호(`{ ... }`) 내의 토큰은 경로가 일치하는 경우 바인딩될 *경로 매개 변수*를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2638">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="57132-2639">경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 분리되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2639">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="57132-2640">예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2640">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="57132-2641">이러한 경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2641">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="57132-2642">경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2642">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="57132-2643">텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2643">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="57132-2644">리터럴 경로 매개 변수 구분 기호(`{` 또는 `}`)와 일치시키려면 문자를 반복하여(`{{` 또는 `}}`) 구분 기호를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2644">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="57132-2645">선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2645">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="57132-2646">예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2646">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="57132-2647">`filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2647">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="57132-2648">URL에 `filename`에 대한 값만 있으면 후행 마침표(`.`)가 선택 사항이므로 경로가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2648">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="57132-2649">다음 URL은 이 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2649">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="57132-2650">별표(`*`) 또는 이중 별표(`**`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2650">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="57132-2651">이러한 매개 변수는 *범용* 매개 변수라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2651">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="57132-2652">예를 들어 `blog/{**slug}`는 `/blog`로 시작하고 모든 값(`slug` 경로 값에 할당된)이 뒤따르는 모든 URI와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2652">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="57132-2653">범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2653">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="57132-2654">catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2654">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="57132-2655">예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2655">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="57132-2656">이스케이프된 슬래시에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2656">Note the escaped forward slash.</span></span> <span data-ttu-id="57132-2657">경로 구분 기호 문자를 왕복하려면 `**` 경로 매개 변수 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2657">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="57132-2658">`{ path = "my/path" }`를 사용하는 경로 `foo/{**path}`는 `foo/my/path`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2658">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="57132-2659">경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 *기본값*이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2659">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="57132-2660">예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2660">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="57132-2661">URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2661">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="57132-2662">경로 매개 변수는 `id?`와 같이 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2662">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="57132-2663">선택적 값과 기본 경로 매개 변수 간의 차이점은 기본값이 있는 경로 매개 변수는 항상 값을 생성한다는 것입니다. 선택적 매개 변수에는 요청 URL에서 값을 제공하는 경우에만 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2663">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="57132-2664">경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2664">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="57132-2665">경로 매개 변수 이름 뒤에 콜론(`:`)과 제약 조건 이름을 추가하여 경로 매개 변수에서 *인라인 제약 조건*을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2665">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="57132-2666">제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에 인수를 괄호(`(...)`)로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2666">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="57132-2667">또 다른 콜론(`:`) 및 제약 조건 이름을 추가하여 여러 인라인 제약 조건을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2667">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="57132-2668">제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2668">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="57132-2669">예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2669">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="57132-2670">경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2670">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="57132-2671">또한 경로 매개 변수에는 링크를 생성하고 URL에 대한 작업 및 페이지와 일치할 때 매개 변수 값을 변환하는 매개 변수 변환기가 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2671">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="57132-2672">제약 조건과 마찬가지로, 매개 변수 변환기는 경로 매개 변수 이름 뒤에 콜론(`:`)과 변환기 이름을 추가하여 라우트 매개 변수에 인라인으로 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2672">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="57132-2673">예를 들어 경로 템플릿 `blog/{article:slugify}`는 `slugify` 변환기를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2673">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="57132-2674">매개 변수 변환기에 대한 자세한 내용은 [매개 변수 변환기 참조](#parameter-transformer-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-2674">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="57132-2675">다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-2675">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="57132-2676">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="57132-2676">Route Template</span></span>                           | <span data-ttu-id="57132-2677">URI 일치 예제</span><span class="sxs-lookup"><span data-stu-id="57132-2677">Example Matching URI</span></span>    | <span data-ttu-id="57132-2678">요청 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="57132-2678">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="57132-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2680">'Blazor'</span></span>
- <span data-ttu-id="57132-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2681">'Identity'</span></span>
- <span data-ttu-id="57132-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2683">'Razor'</span></span>
- <span data-ttu-id="57132-2684">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2686">'Blazor'</span></span>
- <span data-ttu-id="57132-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2687">'Identity'</span></span>
- <span data-ttu-id="57132-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2689">'Razor'</span></span>
- <span data-ttu-id="57132-2690">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2692">'Blazor'</span></span>
- <span data-ttu-id="57132-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2693">'Identity'</span></span>
- <span data-ttu-id="57132-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2695">'Razor'</span></span>
- <span data-ttu-id="57132-2696">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2698">'Blazor'</span></span>
- <span data-ttu-id="57132-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2699">'Identity'</span></span>
- <span data-ttu-id="57132-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2701">'Razor'</span></span>
- <span data-ttu-id="57132-2702">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2704">'Blazor'</span></span>
- <span data-ttu-id="57132-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2705">'Identity'</span></span>
- <span data-ttu-id="57132-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2707">'Razor'</span></span>
- <span data-ttu-id="57132-2708">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2710">'Blazor'</span></span>
- <span data-ttu-id="57132-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2711">'Identity'</span></span>
- <span data-ttu-id="57132-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2713">'Razor'</span></span>
- <span data-ttu-id="57132-2714">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2716">'Blazor'</span></span>
- <span data-ttu-id="57132-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2717">'Identity'</span></span>
- <span data-ttu-id="57132-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2719">'Razor'</span></span>
- <span data-ttu-id="57132-2720">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2722">'Blazor'</span></span>
- <span data-ttu-id="57132-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2723">'Identity'</span></span>
- <span data-ttu-id="57132-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2725">'Razor'</span></span>
- <span data-ttu-id="57132-2726">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2728">'Blazor'</span></span>
- <span data-ttu-id="57132-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2729">'Identity'</span></span>
- <span data-ttu-id="57132-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2731">'Razor'</span></span>
- <span data-ttu-id="57132-2732">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2734">'Blazor'</span></span>
- <span data-ttu-id="57132-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2735">'Identity'</span></span>
- <span data-ttu-id="57132-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2737">'Razor'</span></span>
- <span data-ttu-id="57132-2738">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2740">'Blazor'</span></span>
- <span data-ttu-id="57132-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2741">'Identity'</span></span>
- <span data-ttu-id="57132-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2743">'Razor'</span></span>
- <span data-ttu-id="57132-2744">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2746">'Blazor'</span></span>
- <span data-ttu-id="57132-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2747">'Identity'</span></span>
- <span data-ttu-id="57132-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2749">'Razor'</span></span>
- <span data-ttu-id="57132-2750">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2752">'Blazor'</span></span>
- <span data-ttu-id="57132-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2753">'Identity'</span></span>
- <span data-ttu-id="57132-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2755">'Razor'</span></span>
- <span data-ttu-id="57132-2756">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2758">'Blazor'</span></span>
- <span data-ttu-id="57132-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2759">'Identity'</span></span>
- <span data-ttu-id="57132-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2761">'Razor'</span></span>
- <span data-ttu-id="57132-2762">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2764">'Blazor'</span></span>
- <span data-ttu-id="57132-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2765">'Identity'</span></span>
- <span data-ttu-id="57132-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2767">'Razor'</span></span>
- <span data-ttu-id="57132-2768">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2770">'Blazor'</span></span>
- <span data-ttu-id="57132-2771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2771">'Identity'</span></span>
- <span data-ttu-id="57132-2772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2772">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2773">'Razor'</span></span>
- <span data-ttu-id="57132-2774">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2776">'Blazor'</span></span>
- <span data-ttu-id="57132-2777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2777">'Identity'</span></span>
- <span data-ttu-id="57132-2778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2778">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2779">'Razor'</span></span>
- <span data-ttu-id="57132-2780">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2780">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2782">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2782">'Blazor'</span></span>
- <span data-ttu-id="57132-2783">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2783">'Identity'</span></span>
- <span data-ttu-id="57132-2784">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2784">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2785">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2785">'Razor'</span></span>
- <span data-ttu-id="57132-2786">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2786">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2788">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2788">'Blazor'</span></span>
- <span data-ttu-id="57132-2789">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2789">'Identity'</span></span>
- <span data-ttu-id="57132-2790">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2790">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2791">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2791">'Razor'</span></span>
- <span data-ttu-id="57132-2792">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2792">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2794">'Blazor'</span></span>
- <span data-ttu-id="57132-2795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2795">'Identity'</span></span>
- <span data-ttu-id="57132-2796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2796">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2797">'Razor'</span></span>
- <span data-ttu-id="57132-2798">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2800">'Blazor'</span></span>
- <span data-ttu-id="57132-2801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2801">'Identity'</span></span>
- <span data-ttu-id="57132-2802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2802">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2803">'Razor'</span></span>
- <span data-ttu-id="57132-2804">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2806">'Blazor'</span></span>
- <span data-ttu-id="57132-2807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2807">'Identity'</span></span>
- <span data-ttu-id="57132-2808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2808">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2809">'Razor'</span></span>
- <span data-ttu-id="57132-2810">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2812">'Blazor'</span></span>
- <span data-ttu-id="57132-2813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2813">'Identity'</span></span>
- <span data-ttu-id="57132-2814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2814">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2815">'Razor'</span></span>
- <span data-ttu-id="57132-2816">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2816">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2818">'Blazor'</span></span>
- <span data-ttu-id="57132-2819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2819">'Identity'</span></span>
- <span data-ttu-id="57132-2820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2820">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2821">'Razor'</span></span>
- <span data-ttu-id="57132-2822">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2824">'Blazor'</span></span>
- <span data-ttu-id="57132-2825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2825">'Identity'</span></span>
- <span data-ttu-id="57132-2826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2826">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2827">'Razor'</span></span>
- <span data-ttu-id="57132-2828">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2830">'Blazor'</span></span>
- <span data-ttu-id="57132-2831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2831">'Identity'</span></span>
- <span data-ttu-id="57132-2832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2832">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2833">'Razor'</span></span>
- <span data-ttu-id="57132-2834">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2836">'Blazor'</span></span>
- <span data-ttu-id="57132-2837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2837">'Identity'</span></span>
- <span data-ttu-id="57132-2838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2838">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2839">'Razor'</span></span>
- <span data-ttu-id="57132-2840">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2840">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2842">'Blazor'</span></span>
- <span data-ttu-id="57132-2843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2843">'Identity'</span></span>
- <span data-ttu-id="57132-2844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2844">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2845">'Razor'</span></span>
- <span data-ttu-id="57132-2846">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2848">'Blazor'</span></span>
- <span data-ttu-id="57132-2849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2849">'Identity'</span></span>
- <span data-ttu-id="57132-2850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2850">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2851">'Razor'</span></span>
- <span data-ttu-id="57132-2852">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2854">'Blazor'</span></span>
- <span data-ttu-id="57132-2855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2855">'Identity'</span></span>
- <span data-ttu-id="57132-2856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2856">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2857">'Razor'</span></span>
- <span data-ttu-id="57132-2858">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2860">'Blazor'</span></span>
- <span data-ttu-id="57132-2861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2861">'Identity'</span></span>
- <span data-ttu-id="57132-2862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2862">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2863">'Razor'</span></span>
- <span data-ttu-id="57132-2864">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2866">'Blazor'</span></span>
- <span data-ttu-id="57132-2867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2867">'Identity'</span></span>
- <span data-ttu-id="57132-2868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2868">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2869">'Razor'</span></span>
- <span data-ttu-id="57132-2870">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2872">'Blazor'</span></span>
- <span data-ttu-id="57132-2873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2873">'Identity'</span></span>
- <span data-ttu-id="57132-2874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2874">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2875">'Razor'</span></span>
- <span data-ttu-id="57132-2876">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2878">'Blazor'</span></span>
- <span data-ttu-id="57132-2879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2879">'Identity'</span></span>
- <span data-ttu-id="57132-2880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2880">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2881">'Razor'</span></span>
- <span data-ttu-id="57132-2882">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2884">'Blazor'</span></span>
- <span data-ttu-id="57132-2885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2885">'Identity'</span></span>
- <span data-ttu-id="57132-2886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2886">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2887">'Razor'</span></span>
- <span data-ttu-id="57132-2888">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2890">'Blazor'</span></span>
- <span data-ttu-id="57132-2891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2891">'Identity'</span></span>
- <span data-ttu-id="57132-2892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2892">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2893">'Razor'</span></span>
- <span data-ttu-id="57132-2894">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2896">'Blazor'</span></span>
- <span data-ttu-id="57132-2897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2897">'Identity'</span></span>
- <span data-ttu-id="57132-2898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2898">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2899">'Razor'</span></span>
- <span data-ttu-id="57132-2900">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2902">'Blazor'</span></span>
- <span data-ttu-id="57132-2903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2903">'Identity'</span></span>
- <span data-ttu-id="57132-2904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2904">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2905">'Razor'</span></span>
- <span data-ttu-id="57132-2906">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2906">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2908">'Blazor'</span></span>
- <span data-ttu-id="57132-2909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2909">'Identity'</span></span>
- <span data-ttu-id="57132-2910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2910">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2911">'Razor'</span></span>
- <span data-ttu-id="57132-2912">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2914">'Blazor'</span></span>
- <span data-ttu-id="57132-2915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2915">'Identity'</span></span>
- <span data-ttu-id="57132-2916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2916">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2917">'Razor'</span></span>
- <span data-ttu-id="57132-2918">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2920">'Blazor'</span></span>
- <span data-ttu-id="57132-2921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2921">'Identity'</span></span>
- <span data-ttu-id="57132-2922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2922">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2923">'Razor'</span></span>
- <span data-ttu-id="57132-2924">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2926">'Blazor'</span></span>
- <span data-ttu-id="57132-2927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2927">'Identity'</span></span>
- <span data-ttu-id="57132-2928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2928">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2929">'Razor'</span></span>
- <span data-ttu-id="57132-2930">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2932">'Blazor'</span></span>
- <span data-ttu-id="57132-2933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2933">'Identity'</span></span>
- <span data-ttu-id="57132-2934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2934">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2935">'Razor'</span></span>
- <span data-ttu-id="57132-2936">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2938">'Blazor'</span></span>
- <span data-ttu-id="57132-2939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2939">'Identity'</span></span>
- <span data-ttu-id="57132-2940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2940">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2941">'Razor'</span></span>
- <span data-ttu-id="57132-2942">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2944">'Blazor'</span></span>
- <span data-ttu-id="57132-2945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2945">'Identity'</span></span>
- <span data-ttu-id="57132-2946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2946">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2947">'Razor'</span></span>
- <span data-ttu-id="57132-2948">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2950">'Blazor'</span></span>
- <span data-ttu-id="57132-2951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2951">'Identity'</span></span>
- <span data-ttu-id="57132-2952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2952">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2953">'Razor'</span></span>
- <span data-ttu-id="57132-2954">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2956">'Blazor'</span></span>
- <span data-ttu-id="57132-2957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2957">'Identity'</span></span>
- <span data-ttu-id="57132-2958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2958">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2959">'Razor'</span></span>
- <span data-ttu-id="57132-2960">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2962">'Blazor'</span></span>
- <span data-ttu-id="57132-2963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2963">'Identity'</span></span>
- <span data-ttu-id="57132-2964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2964">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2965">'Razor'</span></span>
- <span data-ttu-id="57132-2966">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2968">'Blazor'</span></span>
- <span data-ttu-id="57132-2969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2969">'Identity'</span></span>
- <span data-ttu-id="57132-2970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2970">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2971">'Razor'</span></span>
- <span data-ttu-id="57132-2972">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2974">'Blazor'</span></span>
- <span data-ttu-id="57132-2975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2975">'Identity'</span></span>
- <span data-ttu-id="57132-2976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2976">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2977">'Razor'</span></span>
- <span data-ttu-id="57132-2978">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2980">'Blazor'</span></span>
- <span data-ttu-id="57132-2981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2981">'Identity'</span></span>
- <span data-ttu-id="57132-2982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2982">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2983">'Razor'</span></span>
- <span data-ttu-id="57132-2984">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2986">'Blazor'</span></span>
- <span data-ttu-id="57132-2987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2987">'Identity'</span></span>
- <span data-ttu-id="57132-2988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2988">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2989">'Razor'</span></span>
- <span data-ttu-id="57132-2990">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2992">'Blazor'</span></span>
- <span data-ttu-id="57132-2993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2993">'Identity'</span></span>
- <span data-ttu-id="57132-2994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-2994">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-2995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-2995">'Razor'</span></span>
- <span data-ttu-id="57132-2996">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-2996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-2998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-2998">'Blazor'</span></span>
- <span data-ttu-id="57132-2999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-2999">'Identity'</span></span>
- <span data-ttu-id="57132-3000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3000">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3001">'Razor'</span></span>
- <span data-ttu-id="57132-3002">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3004">'Blazor'</span></span>
- <span data-ttu-id="57132-3005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3005">'Identity'</span></span>
- <span data-ttu-id="57132-3006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3006">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3007">'Razor'</span></span>
- <span data-ttu-id="57132-3008">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3008">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3010">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3010">'Blazor'</span></span>
- <span data-ttu-id="57132-3011">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3011">'Identity'</span></span>
- <span data-ttu-id="57132-3012">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3012">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3013">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3013">'Razor'</span></span>
- <span data-ttu-id="57132-3014">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3014">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3016">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3016">'Blazor'</span></span>
- <span data-ttu-id="57132-3017">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3017">'Identity'</span></span>
- <span data-ttu-id="57132-3018">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3018">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3019">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3019">'Razor'</span></span>
- <span data-ttu-id="57132-3020">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3020">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3022">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3022">'Blazor'</span></span>
- <span data-ttu-id="57132-3023">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3023">'Identity'</span></span>
- <span data-ttu-id="57132-3024">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3024">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3025">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3025">'Razor'</span></span>
- <span data-ttu-id="57132-3026">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3026">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3028">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3028">'Blazor'</span></span>
- <span data-ttu-id="57132-3029">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3029">'Identity'</span></span>
- <span data-ttu-id="57132-3030">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3030">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3031">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3031">'Razor'</span></span>
- <span data-ttu-id="57132-3032">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3032">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3034">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3034">'Blazor'</span></span>
- <span data-ttu-id="57132-3035">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3035">'Identity'</span></span>
- <span data-ttu-id="57132-3036">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3036">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3037">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3037">'Razor'</span></span>
- <span data-ttu-id="57132-3038">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3038">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3040">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3040">'Blazor'</span></span>
- <span data-ttu-id="57132-3041">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3041">'Identity'</span></span>
- <span data-ttu-id="57132-3042">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3042">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3043">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3043">'Razor'</span></span>
- <span data-ttu-id="57132-3044">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3044">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3046">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3046">'Blazor'</span></span>
- <span data-ttu-id="57132-3047">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3047">'Identity'</span></span>
- <span data-ttu-id="57132-3048">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3048">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3049">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3049">'Razor'</span></span>
- <span data-ttu-id="57132-3050">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3050">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3051">------------------------------------- | | `hello`                                  | `/hello`                | 단일 경로 `/hello`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3051">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="57132-3052">| | `{Page=Home}`                            | `/`                     | `Page`를 찾아 `Home`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3052">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="57132-3053">| | `{Page=Home}`                            | `/Contact`              | `Page`를 찾아 `Contact`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3053">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="57132-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="57132-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="57132-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="57132-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다(`id`가 무시됨).</span><span class="sxs-lookup"><span data-stu-id="57132-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="57132-3057">템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3057">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="57132-3058">제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3058">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="57132-3059">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3059">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="57132-3060">예약된 라우팅 이름</span><span class="sxs-lookup"><span data-stu-id="57132-3060">Reserved routing names</span></span>

<span data-ttu-id="57132-3061">다음 키워드는 예약된 이름이므로 경로 이름 또는 매개 변수로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3061">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="57132-3062">경로 제약 조건 참조</span><span class="sxs-lookup"><span data-stu-id="57132-3062">Route constraint reference</span></span>

<span data-ttu-id="57132-3063">경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3063">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="57132-3064">일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 예/아니요 의사 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3064">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="57132-3065">일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3065">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="57132-3066">예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3066">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="57132-3067">제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3067">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="57132-3068">제약 조건을 **입력 유효성 검사**에 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3068">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="57132-3069">제약 조건이 **입력 유효성 검사**에 사용되는 경우 잘못된 입력으로 인해 적절한 오류 메시지와 함께 *400 - 잘못된 요청* 대신 *404 - 찾을 수 없음* 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3069">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="57132-3070">경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 **명확하게 구분하는 데** 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3070">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="57132-3071">다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3071">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="57132-3072">제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-3072">constraint</span></span> | <span data-ttu-id="57132-3073">예제</span><span class="sxs-lookup"><span data-stu-id="57132-3073">Example</span></span> | <span data-ttu-id="57132-3074">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="57132-3074">Example Matches</span></span> | <span data-ttu-id="57132-3075">참고</span><span class="sxs-lookup"><span data-stu-id="57132-3075">Notes</span></span> |
| ---
<span data-ttu-id="57132-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3077">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3077">'Blazor'</span></span>
- <span data-ttu-id="57132-3078">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3078">'Identity'</span></span>
- <span data-ttu-id="57132-3079">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3079">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3080">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3080">'Razor'</span></span>
- <span data-ttu-id="57132-3081">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3081">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3083">'Blazor'</span></span>
- <span data-ttu-id="57132-3084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3084">'Identity'</span></span>
- <span data-ttu-id="57132-3085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3085">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3086">'Razor'</span></span>
- <span data-ttu-id="57132-3087">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3089">'Blazor'</span></span>
- <span data-ttu-id="57132-3090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3090">'Identity'</span></span>
- <span data-ttu-id="57132-3091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3091">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3092">'Razor'</span></span>
- <span data-ttu-id="57132-3093">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3093">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3095">'Blazor'</span></span>
- <span data-ttu-id="57132-3096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3096">'Identity'</span></span>
- <span data-ttu-id="57132-3097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3097">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3098">'Razor'</span></span>
- <span data-ttu-id="57132-3099">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3099">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3101">'Blazor'</span></span>
- <span data-ttu-id="57132-3102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3102">'Identity'</span></span>
- <span data-ttu-id="57132-3103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3103">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3104">'Razor'</span></span>
- <span data-ttu-id="57132-3105">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3107">'Blazor'</span></span>
- <span data-ttu-id="57132-3108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3108">'Identity'</span></span>
- <span data-ttu-id="57132-3109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3109">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3110">'Razor'</span></span>
- <span data-ttu-id="57132-3111">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3113">'Blazor'</span></span>
- <span data-ttu-id="57132-3114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3114">'Identity'</span></span>
- <span data-ttu-id="57132-3115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3115">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3116">'Razor'</span></span>
- <span data-ttu-id="57132-3117">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3119">'Blazor'</span></span>
- <span data-ttu-id="57132-3120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3120">'Identity'</span></span>
- <span data-ttu-id="57132-3121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3121">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3122">'Razor'</span></span>
- <span data-ttu-id="57132-3123">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3125">'Blazor'</span></span>
- <span data-ttu-id="57132-3126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3126">'Identity'</span></span>
- <span data-ttu-id="57132-3127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3127">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3128">'Razor'</span></span>
- <span data-ttu-id="57132-3129">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3129">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer.</span></span> <span data-ttu-id="57132-3131">| | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3131">| | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | Used to enforce that a non-parameter value is present during URL generation.</span></span> |

<span data-ttu-id="57132-3132">콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3132">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="57132-3133">예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3133">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="57132-3134">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3134">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="57132-3135">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3135">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="57132-3136">프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3136">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="57132-3137">URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3137">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="57132-3138">예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3138">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="57132-3139">정규식</span><span class="sxs-lookup"><span data-stu-id="57132-3139">Regular expressions</span></span>

<span data-ttu-id="57132-3140">ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3140">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="57132-3141">이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3141">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="57132-3142">정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3142">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="57132-3143">정규식 토큰은 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3143">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="57132-3144">라우팅에서 정규식 `^\d{3}-\d{2}-\d{4}$`을 사용하려면:</span><span class="sxs-lookup"><span data-stu-id="57132-3144">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing:</span></span>

* <span data-ttu-id="57132-3145">식에는 문자열에 제공된 단일 백슬래시 `\` 문자가 소스 코드의 이중 백슬래시 `\\` 문자로 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3145">The expression must have the single backslash `\` characters provided in the string as double backslash `\\` characters in the source code.</span></span>
* <span data-ttu-id="57132-3146">정규식에서는 `\` 문자열 이스케이프 문자를 이스케이프하기 위해 `\\`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3146">The regular expression must us `\\` in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="57132-3147">정규식에서 [축자 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string)을 사용하는 경우 `\\`가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3147">The regular expression doesn't require `\\` when using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="57132-3148">라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(`{{`, `}`, `[[`, `]]`).</span><span class="sxs-lookup"><span data-stu-id="57132-3148">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression `{{`, `}`, `[[`, `]]`.</span></span> <span data-ttu-id="57132-3149">다음 표는 정규식 및 이스케이프된 버전을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3149">The following table shows a regular expression and the escaped version:</span></span>

| <span data-ttu-id="57132-3150">정규식</span><span class="sxs-lookup"><span data-stu-id="57132-3150">Regular Expression</span></span>    | <span data-ttu-id="57132-3151">이스케이프된 정규식</span><span class="sxs-lookup"><span data-stu-id="57132-3151">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="57132-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3153">'Blazor'</span></span>
- <span data-ttu-id="57132-3154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3154">'Identity'</span></span>
- <span data-ttu-id="57132-3155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3155">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3156">'Razor'</span></span>
- <span data-ttu-id="57132-3157">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3159">'Blazor'</span></span>
- <span data-ttu-id="57132-3160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3160">'Identity'</span></span>
- <span data-ttu-id="57132-3161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3161">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3162">'Razor'</span></span>
- <span data-ttu-id="57132-3163">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3165">'Blazor'</span></span>
- <span data-ttu-id="57132-3166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3166">'Identity'</span></span>
- <span data-ttu-id="57132-3167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3167">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3168">'Razor'</span></span>
- <span data-ttu-id="57132-3169">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3171">'Blazor'</span></span>
- <span data-ttu-id="57132-3172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3172">'Identity'</span></span>
- <span data-ttu-id="57132-3173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3173">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3174">'Razor'</span></span>
- <span data-ttu-id="57132-3175">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3177">'Blazor'</span></span>
- <span data-ttu-id="57132-3178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3178">'Identity'</span></span>
- <span data-ttu-id="57132-3179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3179">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3180">'Razor'</span></span>
- <span data-ttu-id="57132-3181">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3183">'Blazor'</span></span>
- <span data-ttu-id="57132-3184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3184">'Identity'</span></span>
- <span data-ttu-id="57132-3185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3185">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3186">'Razor'</span></span>
- <span data-ttu-id="57132-3187">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3189">'Blazor'</span></span>
- <span data-ttu-id="57132-3190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3190">'Identity'</span></span>
- <span data-ttu-id="57132-3191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3191">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3192">'Razor'</span></span>
- <span data-ttu-id="57132-3193">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3195">'Blazor'</span></span>
- <span data-ttu-id="57132-3196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3196">'Identity'</span></span>
- <span data-ttu-id="57132-3197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3197">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3198">'Razor'</span></span>
- <span data-ttu-id="57132-3199">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3199">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3201">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3201">'Blazor'</span></span>
- <span data-ttu-id="57132-3202">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3202">'Identity'</span></span>
- <span data-ttu-id="57132-3203">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3203">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3204">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3204">'Razor'</span></span>
- <span data-ttu-id="57132-3205">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3205">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3207">'Blazor'</span></span>
- <span data-ttu-id="57132-3208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3208">'Identity'</span></span>
- <span data-ttu-id="57132-3209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3209">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3210">'Razor'</span></span>
- <span data-ttu-id="57132-3211">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3211">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3213">'Blazor'</span></span>
- <span data-ttu-id="57132-3214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3214">'Identity'</span></span>
- <span data-ttu-id="57132-3215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3215">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3216">'Razor'</span></span>
- <span data-ttu-id="57132-3217">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3219">'Blazor'</span></span>
- <span data-ttu-id="57132-3220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3220">'Identity'</span></span>
- <span data-ttu-id="57132-3221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3221">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3222">'Razor'</span></span>
- <span data-ttu-id="57132-3223">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3225">'Blazor'</span></span>
- <span data-ttu-id="57132-3226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3226">'Identity'</span></span>
- <span data-ttu-id="57132-3227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3227">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3228">'Razor'</span></span>
- <span data-ttu-id="57132-3229">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3231">'Blazor'</span></span>
- <span data-ttu-id="57132-3232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3232">'Identity'</span></span>
- <span data-ttu-id="57132-3233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3233">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3234">'Razor'</span></span>
- <span data-ttu-id="57132-3235">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3237">'Blazor'</span></span>
- <span data-ttu-id="57132-3238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3238">'Identity'</span></span>
- <span data-ttu-id="57132-3239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3239">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3240">'Razor'</span></span>
- <span data-ttu-id="57132-3241">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3243">'Blazor'</span></span>
- <span data-ttu-id="57132-3244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3244">'Identity'</span></span>
- <span data-ttu-id="57132-3245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3245">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3246">'Razor'</span></span>
- <span data-ttu-id="57132-3247">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3249">'Blazor'</span></span>
- <span data-ttu-id="57132-3250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3250">'Identity'</span></span>
- <span data-ttu-id="57132-3251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3251">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3252">'Razor'</span></span>
- <span data-ttu-id="57132-3253">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3255">'Blazor'</span></span>
- <span data-ttu-id="57132-3256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3256">'Identity'</span></span>
- <span data-ttu-id="57132-3257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3257">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3258">'Razor'</span></span>
- <span data-ttu-id="57132-3259">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3261">'Blazor'</span></span>
- <span data-ttu-id="57132-3262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3262">'Identity'</span></span>
- <span data-ttu-id="57132-3263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3263">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3264">'Razor'</span></span>
- <span data-ttu-id="57132-3265">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3267">'Blazor'</span></span>
- <span data-ttu-id="57132-3268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3268">'Identity'</span></span>
- <span data-ttu-id="57132-3269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3269">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3270">'Razor'</span></span>
- <span data-ttu-id="57132-3271">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3273">'Blazor'</span></span>
- <span data-ttu-id="57132-3274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3274">'Identity'</span></span>
- <span data-ttu-id="57132-3275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3275">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3276">'Razor'</span></span>
- <span data-ttu-id="57132-3277">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3277">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="57132-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="57132-3279">라우팅에 사용되는 정규식은 캐럿(`^`) 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3279">Regular expressions used in routing often start with the caret `^` character and match starting position of the string.</span></span> <span data-ttu-id="57132-3280">식은 달러 기호(`$`) 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3280">The expressions often end with the dollar sign `$` character and match end of the string.</span></span> <span data-ttu-id="57132-3281">`^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3281">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="57132-3282">`^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하는데, 이는 종종 원하는 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3282">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="57132-3283">다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3283">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="57132-3284">식</span><span class="sxs-lookup"><span data-stu-id="57132-3284">Expression</span></span>   | <span data-ttu-id="57132-3285">String</span><span class="sxs-lookup"><span data-stu-id="57132-3285">String</span></span>    | <span data-ttu-id="57132-3286">일치</span><span class="sxs-lookup"><span data-stu-id="57132-3286">Match</span></span> | <span data-ttu-id="57132-3287">주석</span><span class="sxs-lookup"><span data-stu-id="57132-3287">Comment</span></span>               |
| ---
<span data-ttu-id="57132-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3289">'Blazor'</span></span>
- <span data-ttu-id="57132-3290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3290">'Identity'</span></span>
- <span data-ttu-id="57132-3291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3291">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3292">'Razor'</span></span>
- <span data-ttu-id="57132-3293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3295">'Blazor'</span></span>
- <span data-ttu-id="57132-3296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3296">'Identity'</span></span>
- <span data-ttu-id="57132-3297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3297">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3298">'Razor'</span></span>
- <span data-ttu-id="57132-3299">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3301">'Blazor'</span></span>
- <span data-ttu-id="57132-3302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3302">'Identity'</span></span>
- <span data-ttu-id="57132-3303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3303">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3304">'Razor'</span></span>
- <span data-ttu-id="57132-3305">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3307">'Blazor'</span></span>
- <span data-ttu-id="57132-3308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3308">'Identity'</span></span>
- <span data-ttu-id="57132-3309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3309">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3310">'Razor'</span></span>
- <span data-ttu-id="57132-3311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3311">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3313">'Blazor'</span></span>
- <span data-ttu-id="57132-3314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3314">'Identity'</span></span>
- <span data-ttu-id="57132-3315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3315">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3316">'Razor'</span></span>
- <span data-ttu-id="57132-3317">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3319">'Blazor'</span></span>
- <span data-ttu-id="57132-3320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3320">'Identity'</span></span>
- <span data-ttu-id="57132-3321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3321">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3322">'Razor'</span></span>
- <span data-ttu-id="57132-3323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3323">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3325">'Blazor'</span></span>
- <span data-ttu-id="57132-3326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3326">'Identity'</span></span>
- <span data-ttu-id="57132-3327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3327">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3328">'Razor'</span></span>
- <span data-ttu-id="57132-3329">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3331">'Blazor'</span></span>
- <span data-ttu-id="57132-3332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3332">'Identity'</span></span>
- <span data-ttu-id="57132-3333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3333">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3334">'Razor'</span></span>
- <span data-ttu-id="57132-3335">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3337">'Blazor'</span></span>
- <span data-ttu-id="57132-3338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3338">'Identity'</span></span>
- <span data-ttu-id="57132-3339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3339">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3340">'Razor'</span></span>
- <span data-ttu-id="57132-3341">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3343">'Blazor'</span></span>
- <span data-ttu-id="57132-3344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3344">'Identity'</span></span>
- <span data-ttu-id="57132-3345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3345">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3346">'Razor'</span></span>
- <span data-ttu-id="57132-3347">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3349">'Blazor'</span></span>
- <span data-ttu-id="57132-3350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3350">'Identity'</span></span>
- <span data-ttu-id="57132-3351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3351">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3352">'Razor'</span></span>
- <span data-ttu-id="57132-3353">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3355">'Blazor'</span></span>
- <span data-ttu-id="57132-3356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3356">'Identity'</span></span>
- <span data-ttu-id="57132-3357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3357">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3358">'Razor'</span></span>
- <span data-ttu-id="57132-3359">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3361">'Blazor'</span></span>
- <span data-ttu-id="57132-3362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3362">'Identity'</span></span>
- <span data-ttu-id="57132-3363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3363">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3364">'Razor'</span></span>
- <span data-ttu-id="57132-3365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3367">'Blazor'</span></span>
- <span data-ttu-id="57132-3368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3368">'Identity'</span></span>
- <span data-ttu-id="57132-3369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3369">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3370">'Razor'</span></span>
- <span data-ttu-id="57132-3371">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3371">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3372">---------- | | `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | mz        | 예   | 식을 찾습니다.    | | `[a-z]{2}`   | MZ        | 예   | 대/소문자를 구분하지 않습니다.    | | `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$`를 참조하세요. | | `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$`를 참조하세요. |</span><span class="sxs-lookup"><span data-stu-id="57132-3372">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="57132-3373">정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3373">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="57132-3374">가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3374">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="57132-3375">예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3375">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="57132-3376">제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3376">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="57132-3377">알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전(템플릿 내 인라인이 아님)에서 전달되는 제약 조건도 정규식으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3377">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="57132-3378">사용자 지정 경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-3378">Custom route constraints</span></span>

<span data-ttu-id="57132-3379">기본 제공 경로 제약 조건 외에도 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3379">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="57132-3380"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 `Match` 단일 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3380">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="57132-3381">사용자 지정 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 사용하려면 앱의 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3381">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="57132-3382"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 구현과 매핑하는 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3382">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="57132-3383">`Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3383">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="57132-3384">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="57132-3384">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="57132-3385">이제 제약 조건 형식을 등록할 때 지정한 이름을 사용하여 일반적인 방식으로 제약 조건을 경로에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3385">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="57132-3386">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="57132-3386">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="57132-3387">매개 변수 변환기 참조</span><span class="sxs-lookup"><span data-stu-id="57132-3387">Parameter transformer reference</span></span>

<span data-ttu-id="57132-3388">매개 변수 변환기는:</span><span class="sxs-lookup"><span data-stu-id="57132-3388">Parameter transformers:</span></span>

* <span data-ttu-id="57132-3389"><xref:Microsoft.AspNetCore.Routing.Route>에 대한 링크를 생성할 때 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3389">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="57132-3390">`Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3390">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="57132-3391"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3391">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="57132-3392">매개 변수의 경로 값을 가져와서 새 문자열 값으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3392">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="57132-3393">생성된 링크에서 변환된 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3393">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="57132-3394">예를 들어, `Url.Action(new { article = "MyTestArticle" })`을 사용하는 경로 패턴 `blog\{article:slugify}`의 사용자 지정 `slugify` 매개 변수 변환기는 `blog\my-test-article`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3394">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="57132-3395">경로 패턴에서 매개 변수 변환기를 사용하려면 먼저 `Startup.ConfigureServices`의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3395">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="57132-3396">매개 변수 변환기는 프레임워크에 의해 사용되어 엔드포인트가 해결되는 URI를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3396">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="57132-3397">예를 들어 ASP.NET Core MVC는 매개 변수 변환기를 사용하여 `area` , `controller` , `action` 및 `page`와 일치하도록 사용되는 경로 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3397">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="57132-3398">위의 경로를 사용하면 `SubscriptionManagementController.GetAll` 작업이 URI `/subscription-management/get-all`과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3398">With the preceding route, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="57132-3399">매개 변수 변환기는 링크를 생성하는 데 사용되는 경로 값을 변경하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3399">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="57132-3400">예를 들어 `Url.Action("GetAll", "SubscriptionManagement")`는 `/subscription-management/get-all`을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3400">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="57132-3401">ASP.NET Core는 생성된 경로와 함께 매개 변수 변환기를 사용하기 위한 API 규칙을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3401">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="57132-3402">ASP.NET Core MVC에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3402">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="57132-3403">이 규칙은 앱의 모든 특성 경로에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3403">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="57132-3404">매개 변수 변환기는 특성 경로 토큰이 교체될 때 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3404">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="57132-3405">자세한 내용은 [매개 변수 변환기를 사용하여 토큰 교체 사용자 지정](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3405">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="57132-3406"> Pages에는 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3406"> Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="57132-3407">이 규칙은 자동으로 검색된 모든 Razor Pages에 지정된 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3407">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="57132-3408">매개 변수 변환기는 Razor Pages 경로의 폴더와 파일 이름 부분을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3408">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="57132-3409">자세한 내용은 [매개 변수 변환기를 사용하여 페이지 경로 사용자 지정](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3409">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="57132-3410">URL 생성 참조</span><span class="sxs-lookup"><span data-stu-id="57132-3410">URL generation reference</span></span>

<span data-ttu-id="57132-3411">다음 예제에서는 경로 값의 사전 및 <xref:Microsoft.AspNetCore.Routing.RouteCollection>이 지정된 경로에 대한 링크를 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3411">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="57132-3412">위의 샘플 끝부분에서 생성된 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>는 `/package/create/123`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3412">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="57132-3413">사전은 "Track Package Route" 템플릿인 `package/{operation}/{id}`의 `operation` 및 `id` 경로 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3413">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="57132-3414">자세한 내용은 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션의 샘플 코드 또는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3414">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="57132-3415"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 생성자에 대한 두 번째 매개 변수는 *앰비언트 값*의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3415">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="57132-3416">개발자가 요청 컨텍스트 내에서 지정해야 하는 값의 수를 제한하므로 앰비언트 값은 사용하기 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3416">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="57132-3417">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3417">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="57132-3418">ASP.NET Core MVC 앱의 `HomeController`에 대한 `About` 작업에서는 `Index` 작업에 연결하기 위해 컨트롤러 경로 값을 지정할 필요가 없으며, `Home`이라는 앰비언트 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3418">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="57132-3419">매개 변수와 일치하지 않는 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3419">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="57132-3420">명시적으로 제공된 값이 앰비언트 값을 재정의하는 경우에도 앰비언트 값이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3420">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="57132-3421">URL에서 일치는 왼쪽에서 오른쪽으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3421">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="57132-3422">명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3422">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="57132-3423">다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3423">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="57132-3424">앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="57132-3424">Ambient Values</span></span>                     | <span data-ttu-id="57132-3425">명시적 값</span><span class="sxs-lookup"><span data-stu-id="57132-3425">Explicit Values</span></span>                        | <span data-ttu-id="57132-3426">결과</span><span class="sxs-lookup"><span data-stu-id="57132-3426">Result</span></span>                  |
| ---
<span data-ttu-id="57132-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3428">'Blazor'</span></span>
- <span data-ttu-id="57132-3429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3429">'Identity'</span></span>
- <span data-ttu-id="57132-3430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3430">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3431">'Razor'</span></span>
- <span data-ttu-id="57132-3432">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3434">'Blazor'</span></span>
- <span data-ttu-id="57132-3435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3435">'Identity'</span></span>
- <span data-ttu-id="57132-3436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3436">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3437">'Razor'</span></span>
- <span data-ttu-id="57132-3438">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3440">'Blazor'</span></span>
- <span data-ttu-id="57132-3441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3441">'Identity'</span></span>
- <span data-ttu-id="57132-3442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3442">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3443">'Razor'</span></span>
- <span data-ttu-id="57132-3444">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3446">'Blazor'</span></span>
- <span data-ttu-id="57132-3447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3447">'Identity'</span></span>
- <span data-ttu-id="57132-3448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3448">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3449">'Razor'</span></span>
- <span data-ttu-id="57132-3450">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3452">'Blazor'</span></span>
- <span data-ttu-id="57132-3453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3453">'Identity'</span></span>
- <span data-ttu-id="57132-3454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3454">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3455">'Razor'</span></span>
- <span data-ttu-id="57132-3456">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3458">'Blazor'</span></span>
- <span data-ttu-id="57132-3459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3459">'Identity'</span></span>
- <span data-ttu-id="57132-3460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3460">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3461">'Razor'</span></span>
- <span data-ttu-id="57132-3462">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3464">'Blazor'</span></span>
- <span data-ttu-id="57132-3465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3465">'Identity'</span></span>
- <span data-ttu-id="57132-3466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3466">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3467">'Razor'</span></span>
- <span data-ttu-id="57132-3468">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3470">'Blazor'</span></span>
- <span data-ttu-id="57132-3471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3471">'Identity'</span></span>
- <span data-ttu-id="57132-3472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3472">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3473">'Razor'</span></span>
- <span data-ttu-id="57132-3474">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3476">'Blazor'</span></span>
- <span data-ttu-id="57132-3477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3477">'Identity'</span></span>
- <span data-ttu-id="57132-3478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3478">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3479">'Razor'</span></span>
- <span data-ttu-id="57132-3480">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3482">'Blazor'</span></span>
- <span data-ttu-id="57132-3483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3483">'Identity'</span></span>
- <span data-ttu-id="57132-3484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3484">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3485">'Razor'</span></span>
- <span data-ttu-id="57132-3486">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3488">'Blazor'</span></span>
- <span data-ttu-id="57132-3489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3489">'Identity'</span></span>
- <span data-ttu-id="57132-3490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3490">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3491">'Razor'</span></span>
- <span data-ttu-id="57132-3492">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3492">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3494">'Blazor'</span></span>
- <span data-ttu-id="57132-3495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3495">'Identity'</span></span>
- <span data-ttu-id="57132-3496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3496">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3497">'Razor'</span></span>
- <span data-ttu-id="57132-3498">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3498">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3500">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3500">'Blazor'</span></span>
- <span data-ttu-id="57132-3501">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3501">'Identity'</span></span>
- <span data-ttu-id="57132-3502">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3502">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3503">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3503">'Razor'</span></span>
- <span data-ttu-id="57132-3504">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3504">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3506">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3506">'Blazor'</span></span>
- <span data-ttu-id="57132-3507">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3507">'Identity'</span></span>
- <span data-ttu-id="57132-3508">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3508">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3509">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3509">'Razor'</span></span>
- <span data-ttu-id="57132-3510">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3510">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3512">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3512">'Blazor'</span></span>
- <span data-ttu-id="57132-3513">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3513">'Identity'</span></span>
- <span data-ttu-id="57132-3514">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3514">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3515">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3515">'Razor'</span></span>
- <span data-ttu-id="57132-3516">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3516">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3518">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3518">'Blazor'</span></span>
- <span data-ttu-id="57132-3519">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3519">'Identity'</span></span>
- <span data-ttu-id="57132-3520">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3520">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3521">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3521">'Razor'</span></span>
- <span data-ttu-id="57132-3522">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3522">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3524">'Blazor'</span></span>
- <span data-ttu-id="57132-3525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3525">'Identity'</span></span>
- <span data-ttu-id="57132-3526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3526">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3527">'Razor'</span></span>
- <span data-ttu-id="57132-3528">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3530">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3530">'Blazor'</span></span>
- <span data-ttu-id="57132-3531">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3531">'Identity'</span></span>
- <span data-ttu-id="57132-3532">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3532">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3533">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3533">'Razor'</span></span>
- <span data-ttu-id="57132-3534">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3534">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3536">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3536">'Blazor'</span></span>
- <span data-ttu-id="57132-3537">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3537">'Identity'</span></span>
- <span data-ttu-id="57132-3538">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3538">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3539">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3539">'Razor'</span></span>
- <span data-ttu-id="57132-3540">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3540">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3542">'Blazor'</span></span>
- <span data-ttu-id="57132-3543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3543">'Identity'</span></span>
- <span data-ttu-id="57132-3544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3544">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3545">'Razor'</span></span>
- <span data-ttu-id="57132-3546">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3548">'Blazor'</span></span>
- <span data-ttu-id="57132-3549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3549">'Identity'</span></span>
- <span data-ttu-id="57132-3550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3550">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3551">'Razor'</span></span>
- <span data-ttu-id="57132-3552">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3554">'Blazor'</span></span>
- <span data-ttu-id="57132-3555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3555">'Identity'</span></span>
- <span data-ttu-id="57132-3556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3556">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3557">'Razor'</span></span>
- <span data-ttu-id="57132-3558">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3560">'Blazor'</span></span>
- <span data-ttu-id="57132-3561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3561">'Identity'</span></span>
- <span data-ttu-id="57132-3562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3562">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3563">'Razor'</span></span>
- <span data-ttu-id="57132-3564">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3566">'Blazor'</span></span>
- <span data-ttu-id="57132-3567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3567">'Identity'</span></span>
- <span data-ttu-id="57132-3568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3568">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3569">'Razor'</span></span>
- <span data-ttu-id="57132-3570">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3572">'Blazor'</span></span>
- <span data-ttu-id="57132-3573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3573">'Identity'</span></span>
- <span data-ttu-id="57132-3574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3574">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3575">'Razor'</span></span>
- <span data-ttu-id="57132-3576">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3578">'Blazor'</span></span>
- <span data-ttu-id="57132-3579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3579">'Identity'</span></span>
- <span data-ttu-id="57132-3580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3580">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3581">'Razor'</span></span>
- <span data-ttu-id="57132-3582">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3584">'Blazor'</span></span>
- <span data-ttu-id="57132-3585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3585">'Identity'</span></span>
- <span data-ttu-id="57132-3586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3586">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3587">'Razor'</span></span>
- <span data-ttu-id="57132-3588">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3590">'Blazor'</span></span>
- <span data-ttu-id="57132-3591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3591">'Identity'</span></span>
- <span data-ttu-id="57132-3592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3592">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3593">'Razor'</span></span>
- <span data-ttu-id="57132-3594">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3594">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3596">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3596">'Blazor'</span></span>
- <span data-ttu-id="57132-3597">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3597">'Identity'</span></span>
- <span data-ttu-id="57132-3598">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3598">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3599">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3599">'Razor'</span></span>
- <span data-ttu-id="57132-3600">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3600">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3602">'Blazor'</span></span>
- <span data-ttu-id="57132-3603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3603">'Identity'</span></span>
- <span data-ttu-id="57132-3604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3604">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3605">'Razor'</span></span>
- <span data-ttu-id="57132-3606">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3608">'Blazor'</span></span>
- <span data-ttu-id="57132-3609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3609">'Identity'</span></span>
- <span data-ttu-id="57132-3610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3610">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3611">'Razor'</span></span>
- <span data-ttu-id="57132-3612">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3614">'Blazor'</span></span>
- <span data-ttu-id="57132-3615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3615">'Identity'</span></span>
- <span data-ttu-id="57132-3616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3616">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3617">'Razor'</span></span>
- <span data-ttu-id="57132-3618">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3618">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3620">'Blazor'</span></span>
- <span data-ttu-id="57132-3621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3621">'Identity'</span></span>
- <span data-ttu-id="57132-3622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3622">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3623">'Razor'</span></span>
- <span data-ttu-id="57132-3624">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3626">'Blazor'</span></span>
- <span data-ttu-id="57132-3627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3627">'Identity'</span></span>
- <span data-ttu-id="57132-3628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3628">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3629">'Razor'</span></span>
- <span data-ttu-id="57132-3630">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3632">'Blazor'</span></span>
- <span data-ttu-id="57132-3633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3633">'Identity'</span></span>
- <span data-ttu-id="57132-3634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3634">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3635">'Razor'</span></span>
- <span data-ttu-id="57132-3636">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3638">'Blazor'</span></span>
- <span data-ttu-id="57132-3639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3639">'Identity'</span></span>
- <span data-ttu-id="57132-3640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3640">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3641">'Razor'</span></span>
- <span data-ttu-id="57132-3642">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3644">'Blazor'</span></span>
- <span data-ttu-id="57132-3645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3645">'Identity'</span></span>
- <span data-ttu-id="57132-3646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3646">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3647">'Razor'</span></span>
- <span data-ttu-id="57132-3648">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3650">'Blazor'</span></span>
- <span data-ttu-id="57132-3651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3651">'Identity'</span></span>
- <span data-ttu-id="57132-3652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3652">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3653">'Razor'</span></span>
- <span data-ttu-id="57132-3654">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3656">'Blazor'</span></span>
- <span data-ttu-id="57132-3657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3657">'Identity'</span></span>
- <span data-ttu-id="57132-3658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3658">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3659">'Razor'</span></span>
- <span data-ttu-id="57132-3660">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3662">'Blazor'</span></span>
- <span data-ttu-id="57132-3663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3663">'Identity'</span></span>
- <span data-ttu-id="57132-3664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3664">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3665">'Razor'</span></span>
- <span data-ttu-id="57132-3666">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3668">'Blazor'</span></span>
- <span data-ttu-id="57132-3669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3669">'Identity'</span></span>
- <span data-ttu-id="57132-3670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3670">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3671">'Razor'</span></span>
- <span data-ttu-id="57132-3672">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3672">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3673">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="57132-3673">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="57132-3674">경로에 매개 변수에 해당하지 않고 값이 명시적으로 제공된 기본값이 있는 경우 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3674">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="57132-3675">`controller` 및 `action`에 대해 일치하는 값이 제공되는 경우에만 링크 생성에서 이 경로에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3675">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="57132-3676">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="57132-3676">Complex segments</span></span>

<span data-ttu-id="57132-3677">복잡한 세그먼트(예: `[Route("/x{token}y")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3677">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="57132-3678">복잡한 세그먼트 일치 방법에 대한 자세한 설명은 [이 코드](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3678">See [this code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="57132-3679">[코드 샘플](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)은 ASP.NET Core에서 사용되지 않지만 복잡한 세그먼트에 대한 적절한 설명을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3679">The [code sample](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57132-3680">라우팅은 요청 URI를 경로 처리기에 매핑하고 들어오는 요청을 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3680">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="57132-3681">경로는 앱에서 정의되고 앱 시작 시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3681">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="57132-3682">경로는 요청에 포함된 URL에서 필요에 따라 값을 추출할 수 있으며 이러한 값은 요청 처리를 위해 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3682">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="57132-3683">라우팅은 앱에 구성된 경로를 사용하여 경로 처리기에 매핑되는 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3683">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="57132-3684">ASP.NET Core 2.1에서 최신 라우팅 시나리오를 사용하려면 `Startup.ConfigureServices`의 MVC 서비스 등록에 [호환성 버전](xref:mvc/compatibility-version)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3684">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="57132-3685">이 문서에서는 낮은 수준의 ASP.NET Core 라우팅을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3685">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="57132-3686">ASP.NET Core MVC 라우팅에 대한 내용은 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3686">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="57132-3687">Razor Pages의 라우팅 규칙에 대한 내용은 <xref:razor-pages/razor-pages-conventions>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3687">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="57132-3688">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57132-3688">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="57132-3689">라우팅 기본 사항</span><span class="sxs-lookup"><span data-stu-id="57132-3689">Routing basics</span></span>

<span data-ttu-id="57132-3690">대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3690">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="57132-3691">기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:</span><span class="sxs-lookup"><span data-stu-id="57132-3691">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="57132-3692">기본적이고 서술적인 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3692">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="57132-3693">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3693">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="57132-3694">일반적으로 개발자는 [특성 라우팅](xref:mvc/controllers/routing#attribute-routing) 또는 전용 기존 경로를 사용하여 특수한 상황에서 앱의 트래픽이 높은 영역(예: 블로그 및 전자 상거래 엔드포인트)에 간결한 추가 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3694">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="57132-3695">Web API는 특성 라우팅을 사용하여 작업이 HTTP 동사로 표현되는 리소스 집합으로 앱의 기능을 모델링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3695">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="57132-3696">이는 동일한 논리 리소스의 많은 작업(예: GET, POST)이 동일한 URL을 사용한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3696">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="57132-3697">특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3697">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="57132-3698"> Pages 앱은 기본 기존 라우팅을 사용하여 앱의 *Pages* 폴더에서 명명된 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3698"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="57132-3699">Razor Pages 라우팅 동작을 사용자 지정할 수 있는 추가 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3699">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="57132-3700">자세한 내용은 <xref:razor-pages/index> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3700">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="57132-3701">URL 생성 지원을 사용하면 URL을 하드 코딩하지 않고 앱을 개발하여 앱을 서로 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3701">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="57132-3702">이 지원을 사용하면 기본 라우팅 구성으로 시작하고 앱의 리소스 레이아웃이 결정된 후에 해당 경로를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3702">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="57132-3703">라우팅은 <xref:Microsoft.AspNetCore.Routing.IRouter>의 경로 구현을 사용하여 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3703">Routing uses routes implementations of <xref:Microsoft.AspNetCore.Routing.IRouter> to:</span></span>

* <span data-ttu-id="57132-3704">들어오는 요청을 *경로 처리기*에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3704">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="57132-3705">응답에 사용되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3705">Generate the URLs used in responses.</span></span>

<span data-ttu-id="57132-3706">기본적으로 앱에는 단일 경로 컬렉션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3706">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="57132-3707">요청이 도착하면 컬렉션의 경로가 컬렉션에 존재하는 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3707">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="57132-3708">프레임워크는 컬렉션의 각 경로에서 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출하여 들어오는 요청 URL을 컬렉션의 경로와 일치시키려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3708">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="57132-3709">응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3709">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="57132-3710">라우팅 시스템에는 다음과 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3710">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="57132-3711">경로 템플릿 구문은 토큰화된 경로 매개 변수를 사용하여 경로를 정의하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3711">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="57132-3712">기본 방식 스타일 및 특성 스타일 엔드포인트 구성이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3712">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="57132-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint>는 URL 매개 변수가 지정한 엔드포인트 제약 조건에 대해 유효한 값을 포함하고 있는지 알아내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="57132-3714">MVC/Razor Pages와 같은 앱 모델은 라우팅 시나리오의 예측 가능한 구현을 가진 모든 경로를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3714">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="57132-3715">응답은 라우팅을 사용하여 경로 정보에 따라 URL을 생성(예: 리디렉션 또는 링크)하므로 하드 코딩된 URL을 방지하여 유지 관리에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3715">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="57132-3716">URL 생성은 임의의 확장성을 지원하는 경로를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3716">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="57132-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>는 URL을 작성하는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="57132-3718">라우팅은 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 클래스에 의해 [미들웨어](xref:fundamentals/middleware/index) 파이프라인에 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3718">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="57132-3719">[ASP.NET Core MVC](xref:mvc/overview)는 라우팅을 해당 구성의 일부로 미들웨어 파이프라인에 추가하고, MVC 및 Razor Pages 앱에서 라우팅을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3719">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="57132-3720">라우팅을 독립 실행형 구성 요소로 사용하는 방법을 알아보려면 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3720">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="57132-3721">URL 일치</span><span class="sxs-lookup"><span data-stu-id="57132-3721">URL matching</span></span>

<span data-ttu-id="57132-3722">URL 일치는 라우팅이 들어오는 요청을 *처리기*로 디스패치하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3722">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="57132-3723">이 프로세스는 URL 경로의 데이터를 기반으로 하지만 요청에 있는 모든 데이터를 고려하도록 확장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3723">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="57132-3724">요청을 별도의 처리기로 디스패치하는 기능은 앱의 크기와 복잡성을 확장하는 핵심입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3724">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="57132-3725">들어오는 요청이 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>에 진입하면 순차적으로 각 경로의 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3725">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="57132-3726"><xref:Microsoft.AspNetCore.Routing.IRouter> 인스턴스는 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*)를 null이 아닌 <xref:Microsoft.AspNetCore.Http.RequestDelegate>로 설정하여 요청을 *처리*할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3726">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="57132-3727">경로가 요청에 대한 처리기를 설정하는 경우 경로 처리가 중지되고 해당 처리기가 요청을 처리하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3727">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="57132-3728">요청을 처리할 경로 처리기가 없는 경우 미들웨어는 요청을 요청 파이프라인의 다음 미들웨어에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3728">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="57132-3729"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>에 대한 기본 입력은 현재 요청과 연결된 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3729">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="57132-3730">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 및 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*)는 경로가 일치된 후에 설정되는 출력입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3730">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="57132-3731"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>를 호출하는 일치는 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData)의 속성도 지금까지 수행된 요청 처리에 따라 적절한 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3731">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="57132-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*)는 경로에서 생성된 *경로 값*의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="57132-3733">이러한 값은 일반적으로 URL을 토큰화하여 결정되고, 사용자 입력을 수락하거나 앱 내부의 추가 디스패치 결정을 내리는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3733">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="57132-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)는 일치하는 경로와 관련된 추가 데이터의 속성 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="57132-3735"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>는 앱에서 일치된 경로에 따라 결정할 수 있도록 각 경로와 상태 데이터의 연결을 지원하기 위해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3735"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="57132-3736">이러한 값은 개발자 정의되고 어떤 방식으로든 라우팅의 동작에 영향을 주지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="57132-3736">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="57132-3737">또한 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)에 안전하게 배치되는(stashed) 값은 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values)와는 달리 문자열 간에 변환될 수 있어야 하는 모든 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3737">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="57132-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers)는 요청을 성공적으로 일치하는 데 참여한 경로의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="57132-3739">경로는 서로 중첩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3739">Routes can be nested inside of one another.</span></span> <span data-ttu-id="57132-3740"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 속성은 일치한 경로의 논리 트리를 통해 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3740">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="57132-3741">일반적으로 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 첫 번째 항목은 경로 컬렉션이며 URL 생성을 위해 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3741">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="57132-3742"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers>의 마지막 항목은 일치한 경로 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3742">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="57132-3743">URL 생성</span><span class="sxs-lookup"><span data-stu-id="57132-3743">URL generation</span></span>

<span data-ttu-id="57132-3744">URL 생성은 라우팅이 경로 값의 집합을 기반으로 하는 URL 경로를 만들 수 있는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3744">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="57132-3745">이렇게 하면 경로 처리기와 이에 액세스하는 URL을 논리적으로 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3745">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="57132-3746">URL 생성은 비슷한 반복적인 프로세스를 따르지만 경로 컬렉션의 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드로 호출하는 사용자 또는 프레임워크 코드로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3746">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="57132-3747">각 *경로*에는 null이 아닌 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>가 반환될 때까지 순차적으로 호출되는 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3747">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="57132-3748"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>에 대한 기본 입력은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3748">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="57132-3749">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="57132-3749">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="57132-3750">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="57132-3750">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="57132-3751">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="57132-3751">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="57132-3752">경로는 주로 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 및 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>에서 제공하는 경로 값을 사용하여 URL을 생성할 수 있는지 여부와 포함할 값을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3752">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="57132-3753"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues>는 현재 요청 일치로부터 생성된 경로 값의 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3753">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="57132-3754">반면, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values>는 현재 작업에 대한 원하는 URL을 생성하는 방법을 지정하는 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3754">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="57132-3755"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>는 경로가 서비스 또는 현재 컨텍스트와 연결된 추가 데이터를 가져와야 하는 경우에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3755">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="57132-3756">[VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*)를 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)에 대한 재정의 세트로 간주하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3756">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="57132-3757">URL 생성은 동일한 경로 또는 경로 값을 사용하는 링크에 대한 URL을 생성하기 위해 현재 요청의 경로 값을 다시 사용하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3757">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="57132-3758"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>의 출력은 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3758">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="57132-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 <xref:Microsoft.AspNetCore.Routing.RouteData>와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="57132-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData>는 출력 URL 및 경로에 의해 설정되어야 하는 몇 가지 추가 속성에 대한 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="57132-3761">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 속성은 경로에 의해 생성된 *가상 경로*를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3761">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="57132-3762">필요에 따라 경로를 추가로 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3762">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="57132-3763">HTML에서 생성된 URL을 렌더링하려는 경우 앱의 기본 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3763">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="57132-3764">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*)는 성공적으로 URL을 생성한 경로에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3764">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="57132-3765">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 속성은 URL을 생성한 경로와 관련된 추가 데이터의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3765">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="57132-3766">이 속성은 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3766">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="57132-3767">경로 만들기</span><span class="sxs-lookup"><span data-stu-id="57132-3767">Create routes</span></span>

<span data-ttu-id="57132-3768">라우팅은 <xref:Microsoft.AspNetCore.Routing.IRouter>의 표준 구현으로 <xref:Microsoft.AspNetCore.Routing.Route> 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3768">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="57132-3769"><xref:Microsoft.AspNetCore.Routing.Route>는 *경로 템플릿* 구문을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>가 호출될 때 URL 경로와 일치하는 패턴을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3769"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="57132-3770"><xref:Microsoft.AspNetCore.Routing.Route>는 동일한 경로 템플릿을 사용하여 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>가 호출되었을 때 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3770"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="57132-3771">대부분의 앱은 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 또는 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>에 정의된 유사한 확장 메서드 중 하나를 호출하여 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3771">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="57132-3772">모든 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 확장 메서드는 <xref:Microsoft.AspNetCore.Routing.Route>의 인스턴스를 만들고, 경로 컬렉션에 이를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3772">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="57132-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 경로 처리기 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="57132-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>는 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>에 의해 처리되는 경로만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="57132-3775">기본 처리기는 `IRouter`이며, 처리기에서 요청을 처리하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3775">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="57132-3776">예를 들어 ASP.NET Core MVC는 일반적으로 사용 가능한 컨트롤러 및 작업과 일치하는 요청만 처리하는 기본 처리기로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3776">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="57132-3777">MVC의 라우팅에 대해 자세히 알아보려면 <xref:mvc/controllers/routing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3777">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="57132-3778">다음 코드 예제는 일반적인 ASP.NET Core MVC 경로 정의에서 사용되는 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 호출의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3778">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="57132-3779">이 템플릿은 URL 경로와 일치시키고 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3779">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="57132-3780">예를 들어 `/Products/Details/17` 경로는 `{ controller = Products, action = Details, id = 17 }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3780">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="57132-3781">경로 값은 URL 경로를 세그먼트로 분할하고 각 세그먼트를 경로 템플릿의 *경로 매개 변수* 이름과 일치시켜 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3781">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="57132-3782">경로 매개 변수는 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3782">Route parameters are named.</span></span> <span data-ttu-id="57132-3783">매개 변수는 매개 변수 이름을 `{ ... }` 중괄호로 묶어 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3783">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="57132-3784">또한 위의 템플릿은 `/` URL 경로와 일치시키고 `{ controller = Home, action = Index }` 값을 생성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3784">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="57132-3785">이는 `{controller}` 및 `{action}` 경로 매개 변수에 기본값이 있으며 `id` 경로 매개 변수는 선택 사항이기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3785">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="57132-3786">경로 매개 변수 이름 뒤에 있는 값이 뒤따르는 등호(`=`)는 매개 변수에 대한 기본값을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3786">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="57132-3787">경로 매개 변수 이름 뒤에 있는 물음표(`?`)는 선택적 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3787">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="57132-3788">기본 값을 사용하는 경로 매개 변수는 경로가 일치하는 경우 *항상* 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3788">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="57132-3789">해당 URL 경로 세그먼트가 없는 경우 선택적 매개 변수는 경로 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3789">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="57132-3790">경로 템플릿 시나리오 및 구문에 대한 자세한 설명은 [경로 템플릿 참조](#route-template-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3790">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="57132-3791">다음 예제에서 `{id:int}` 경로 매개 변수 정의는 `id` 경로 매개 변수에 대한 [경로 제약 조건](#route-constraint-reference)을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3791">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="57132-3792">이 템플릿은 `/Products/Details/Apples`가 아닌 `/Products/Details/17`과 같은 URL 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3792">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="57132-3793">경로 제약 조건은 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 구현하고 경로 값을 검사하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3793">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="57132-3794">이 예제에서 경로 값 `id`는 정수로 변환할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3794">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="57132-3795">프레임워크에서 제공하는 경로 제약 조건에 대한 설명은 [경로 제약 조건 참조](#route-constraint-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3795">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="57132-3796"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>의 추가 오버로드는 `constraints`, `dataTokens` 및 `defaults`에 대한 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3796">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="57132-3797">이러한 매개 변수의 일반적인 사용법은 익명 형식의 속성 이름이 경로 매개 변수 이름과 일치하는 익명 형식의 개체를 전달하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3797">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="57132-3798">다음 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 예제에서는 동등한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3798">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="57132-3799">제약 조건 및 기본값을 정의하기 위한 인라인 구문은 단순 경로에 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3799">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="57132-3800">그러나 데이터 토큰과 같이 인라인 구문에서는 지원되지 않는 시나리오가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3800">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="57132-3801">다음 예제에서는 몇 가지 추가 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3801">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="57132-3802">위의 템플릿은 `/Blog/All-About-Routing/Introduction`과 같은 URL 경로와 일치하고 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3802">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="57132-3803">`controller` 및 `action`에 대한 기본 경로 값은 템플릿에 해당 경로 매개 변수가 없는 경우에도 경로에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3803">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="57132-3804">기본값은 경로 템플릿에서 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3804">Default values can be specified in the route template.</span></span> <span data-ttu-id="57132-3805">`article` 경로 매개 변수는 경로 매개 변수 이름 앞에 별표(`*`)를 표시하여 *범용*으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3805">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="57132-3806">범용 경로 매개 변수는 URL 경로의 나머지를 캡처하고 빈 문자열과도 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3806">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="57132-3807">다음 예제에서는 경로 제약 조건 및 데이터 토큰을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3807">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="57132-3808">앞의 템플릿은 `/en-US/Products/5`와 같은 URL 경로와 일치하고, `{ controller = Products, action = Details, id = 5 }` 값 및 `{ locale = en-US }` 데이터 토큰을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3808">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![지역 Windows 토큰](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="57132-3810">Route 클래스 URL 생성</span><span class="sxs-lookup"><span data-stu-id="57132-3810">Route class URL generation</span></span>

<span data-ttu-id="57132-3811"><xref:Microsoft.AspNetCore.Routing.Route> 클래스는 경로 값의 집합을 해당 경로 템플릿과 결합하여 URL 생성을 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3811">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="57132-3812">이는 논리적으로 URL 경로와 일치시키는 역방향 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3812">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="57132-3813">URL 생성을 보다 잘 이해하려면 생성하려는 URL을 가정한 다음, 경로 템플릿을 해당 URL과 일치시키는 방법을 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3813">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="57132-3814">어떤 값이 생성되나요?</span><span class="sxs-lookup"><span data-stu-id="57132-3814">What values would be produced?</span></span> <span data-ttu-id="57132-3815">이는 URL 생성이 <xref:Microsoft.AspNetCore.Routing.Route> 클래스에서 작동하는 방식과 대략적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3815">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="57132-3816">다음 예제에서는 일반적인 ASP.NET Core MVC 기본 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3816">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="57132-3817">`{ controller = Products, action = List }` 경로 값을 사용하면 `/Products/List` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3817">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="57132-3818">경로 값은 URL 경로를 구성하기 위해 해당 경로 매개 변수에 대해 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3818">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="57132-3819">`id`는 선택적 경로 매개 변수이므로 `id`에 대한 값 없이 URL이 성공적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3819">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="57132-3820">`{ controller = Home, action = Index }` 경로 값을 사용하면 `/` URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3820">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="57132-3821">제공된 경로 값이 기본값과 일치하고, 기본값에 해당하는 세그먼트는 안전하게 생략됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3821">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="57132-3822">뒤이어 언급된 경로 정의(`/Home/Index` 및 `/`)를 사용하는 URL 생성 왕복 모두에서는 URL을 생성하기 위해 사용된 것과 동일한 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3822">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="57132-3823">ASP.NET Core MVC를 사용하는 앱은 라우팅을 직접 호출하는 대신 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper>를 사용하여 URL을 생성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3823">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="57132-3824">URL 생성에 대한 자세한 내용은 [URL 생성 참조](#url-generation-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3824">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="57132-3825">라우팅 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="57132-3825">Use routing middleware</span></span>

<span data-ttu-id="57132-3826">앱의 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-3826">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="57132-3827">`Startup.ConfigureServices`의 서비스 컨테이너에 라우팅을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3827">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="57132-3828">경로는 `Startup.Configure` 메서드에서 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3828">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="57132-3829">샘플 앱에서 사용하는 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3829">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="57132-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: HTTP GET 요청만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="57132-3831">다음 표는 지정된 URI에 대한 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-3831">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="57132-3832">URI</span><span class="sxs-lookup"><span data-stu-id="57132-3832">URI</span></span>                    | <span data-ttu-id="57132-3833">응답</span><span class="sxs-lookup"><span data-stu-id="57132-3833">Response</span></span>                                          |
| ---
<span data-ttu-id="57132-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3835">'Blazor'</span></span>
- <span data-ttu-id="57132-3836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3836">'Identity'</span></span>
- <span data-ttu-id="57132-3837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3837">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3838">'Razor'</span></span>
- <span data-ttu-id="57132-3839">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3841">'Blazor'</span></span>
- <span data-ttu-id="57132-3842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3842">'Identity'</span></span>
- <span data-ttu-id="57132-3843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3843">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3844">'Razor'</span></span>
- <span data-ttu-id="57132-3845">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3847">'Blazor'</span></span>
- <span data-ttu-id="57132-3848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3848">'Identity'</span></span>
- <span data-ttu-id="57132-3849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3849">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3850">'Razor'</span></span>
- <span data-ttu-id="57132-3851">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3853">'Blazor'</span></span>
- <span data-ttu-id="57132-3854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3854">'Identity'</span></span>
- <span data-ttu-id="57132-3855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3855">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3856">'Razor'</span></span>
- <span data-ttu-id="57132-3857">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3859">'Blazor'</span></span>
- <span data-ttu-id="57132-3860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3860">'Identity'</span></span>
- <span data-ttu-id="57132-3861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3861">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3862">'Razor'</span></span>
- <span data-ttu-id="57132-3863">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3865">'Blazor'</span></span>
- <span data-ttu-id="57132-3866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3866">'Identity'</span></span>
- <span data-ttu-id="57132-3867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3867">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3868">'Razor'</span></span>
- <span data-ttu-id="57132-3869">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3871">'Blazor'</span></span>
- <span data-ttu-id="57132-3872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3872">'Identity'</span></span>
- <span data-ttu-id="57132-3873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3873">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3874">'Razor'</span></span>
- <span data-ttu-id="57132-3875">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3877">'Blazor'</span></span>
- <span data-ttu-id="57132-3878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3878">'Identity'</span></span>
- <span data-ttu-id="57132-3879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3879">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3880">'Razor'</span></span>
- <span data-ttu-id="57132-3881">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3883">'Blazor'</span></span>
- <span data-ttu-id="57132-3884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3884">'Identity'</span></span>
- <span data-ttu-id="57132-3885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3885">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3886">'Razor'</span></span>
- <span data-ttu-id="57132-3887">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3887">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3889">'Blazor'</span></span>
- <span data-ttu-id="57132-3890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3890">'Identity'</span></span>
- <span data-ttu-id="57132-3891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3891">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3892">'Razor'</span></span>
- <span data-ttu-id="57132-3893">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3895">'Blazor'</span></span>
- <span data-ttu-id="57132-3896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3896">'Identity'</span></span>
- <span data-ttu-id="57132-3897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3897">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3898">'Razor'</span></span>
- <span data-ttu-id="57132-3899">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3901">'Blazor'</span></span>
- <span data-ttu-id="57132-3902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3902">'Identity'</span></span>
- <span data-ttu-id="57132-3903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3903">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3904">'Razor'</span></span>
- <span data-ttu-id="57132-3905">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3907">'Blazor'</span></span>
- <span data-ttu-id="57132-3908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3908">'Identity'</span></span>
- <span data-ttu-id="57132-3909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3909">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3910">'Razor'</span></span>
- <span data-ttu-id="57132-3911">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3913">'Blazor'</span></span>
- <span data-ttu-id="57132-3914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3914">'Identity'</span></span>
- <span data-ttu-id="57132-3915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3915">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3916">'Razor'</span></span>
- <span data-ttu-id="57132-3917">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3917">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3919">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3919">'Blazor'</span></span>
- <span data-ttu-id="57132-3920">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3920">'Identity'</span></span>
- <span data-ttu-id="57132-3921">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3921">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3922">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3922">'Razor'</span></span>
- <span data-ttu-id="57132-3923">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3923">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3925">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3925">'Blazor'</span></span>
- <span data-ttu-id="57132-3926">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3926">'Identity'</span></span>
- <span data-ttu-id="57132-3927">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3927">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3928">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3928">'Razor'</span></span>
- <span data-ttu-id="57132-3929">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3929">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3931">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3931">'Blazor'</span></span>
- <span data-ttu-id="57132-3932">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3932">'Identity'</span></span>
- <span data-ttu-id="57132-3933">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3933">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3934">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3934">'Razor'</span></span>
- <span data-ttu-id="57132-3935">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3935">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3937">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3937">'Blazor'</span></span>
- <span data-ttu-id="57132-3938">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3938">'Identity'</span></span>
- <span data-ttu-id="57132-3939">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3939">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3940">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3940">'Razor'</span></span>
- <span data-ttu-id="57132-3941">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3941">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3943">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3943">'Blazor'</span></span>
- <span data-ttu-id="57132-3944">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3944">'Identity'</span></span>
- <span data-ttu-id="57132-3945">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3945">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3946">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3946">'Razor'</span></span>
- <span data-ttu-id="57132-3947">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3947">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3949">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3949">'Blazor'</span></span>
- <span data-ttu-id="57132-3950">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3950">'Identity'</span></span>
- <span data-ttu-id="57132-3951">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3951">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3952">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3952">'Razor'</span></span>
- <span data-ttu-id="57132-3953">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3953">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3955">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3955">'Blazor'</span></span>
- <span data-ttu-id="57132-3956">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3956">'Identity'</span></span>
- <span data-ttu-id="57132-3957">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3957">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3958">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3958">'Razor'</span></span>
- <span data-ttu-id="57132-3959">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3959">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3961">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3961">'Blazor'</span></span>
- <span data-ttu-id="57132-3962">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3962">'Identity'</span></span>
- <span data-ttu-id="57132-3963">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3963">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3964">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3964">'Razor'</span></span>
- <span data-ttu-id="57132-3965">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3965">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3967">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3967">'Blazor'</span></span>
- <span data-ttu-id="57132-3968">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3968">'Identity'</span></span>
- <span data-ttu-id="57132-3969">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3969">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3970">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3970">'Razor'</span></span>
- <span data-ttu-id="57132-3971">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3971">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3973">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3973">'Blazor'</span></span>
- <span data-ttu-id="57132-3974">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3974">'Identity'</span></span>
- <span data-ttu-id="57132-3975">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3975">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3976">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3976">'Razor'</span></span>
- <span data-ttu-id="57132-3977">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3977">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3979">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3979">'Blazor'</span></span>
- <span data-ttu-id="57132-3980">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3980">'Identity'</span></span>
- <span data-ttu-id="57132-3981">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3981">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3982">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3982">'Razor'</span></span>
- <span data-ttu-id="57132-3983">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3983">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3985">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3985">'Blazor'</span></span>
- <span data-ttu-id="57132-3986">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3986">'Identity'</span></span>
- <span data-ttu-id="57132-3987">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3987">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3988">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3988">'Razor'</span></span>
- <span data-ttu-id="57132-3989">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3989">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3991">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3991">'Blazor'</span></span>
- <span data-ttu-id="57132-3992">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3992">'Identity'</span></span>
- <span data-ttu-id="57132-3993">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3993">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-3994">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-3994">'Razor'</span></span>
- <span data-ttu-id="57132-3995">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-3995">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-3997">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-3997">'Blazor'</span></span>
- <span data-ttu-id="57132-3998">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-3998">'Identity'</span></span>
- <span data-ttu-id="57132-3999">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-3999">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4000">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4000">'Razor'</span></span>
- <span data-ttu-id="57132-4001">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4001">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4003">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4003">'Blazor'</span></span>
- <span data-ttu-id="57132-4004">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4004">'Identity'</span></span>
- <span data-ttu-id="57132-4005">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4005">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4006">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4006">'Razor'</span></span>
- <span data-ttu-id="57132-4007">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4007">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4009">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4009">'Blazor'</span></span>
- <span data-ttu-id="57132-4010">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4010">'Identity'</span></span>
- <span data-ttu-id="57132-4011">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4011">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4012">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4012">'Razor'</span></span>
- <span data-ttu-id="57132-4013">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4013">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4015">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4015">'Blazor'</span></span>
- <span data-ttu-id="57132-4016">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4016">'Identity'</span></span>
- <span data-ttu-id="57132-4017">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4017">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4018">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4018">'Razor'</span></span>
- <span data-ttu-id="57132-4019">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4019">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4020">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-4020">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="57132-4021">경로 값: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-4021">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="57132-4022">경로 값: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="57132-4022">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="57132-4023">경로 값: [operation, track], [id, -3] | | `/package/track/`      | 일치하는 경로가 없으므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4023">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="57132-4024">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="57132-4024">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="57132-4025">| | `POST /hello/Joe`      | HTTP GET만 일치하므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4025">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="57132-4026">| | `GET /hello/Joe/Smith` | 일치하는 경로가 없으므로, 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4026">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="57132-4027">단일 경로를 구성하는 경우 `IRouter` 인스턴스를 전달하는 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4027">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="57132-4028"><xref:Microsoft.AspNetCore.Routing.RouteBuilder>는 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4028">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="57132-4029">프레임워크는 경로를 만드는 확장 메서드 모음(<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4029">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="57132-4030"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>과 같은 나열된 메서드 중 일부는 <xref:Microsoft.AspNetCore.Http.RequestDelegate>를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4030">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="57132-4031"><xref:Microsoft.AspNetCore.Http.RequestDelegate>는 경로가 일치하는 경우 *경로 처리기*로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4031">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="57132-4032">이 모음의 다른 메서드를 사용하면 경로 처리기로 사용할 미들웨어 파이프라인을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4032">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="57132-4033">`Map*` 메서드에서 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>와 같은 처리기를 허용하지 않는 경우 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4033">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="57132-4034">`Map[Verb]` 메서드는 제약 조건을 사용하여 메서드 이름에 지정된 HTTP 동사에 대한 경로로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4034">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="57132-4035">예제는 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 및 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4035">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="57132-4036">경로 템플릿 참조</span><span class="sxs-lookup"><span data-stu-id="57132-4036">Route template reference</span></span>

<span data-ttu-id="57132-4037">중괄호(`{ ... }`) 내의 토큰은 경로가 일치하는 경우 바인딩될 *경로 매개 변수*를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4037">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="57132-4038">경로 세그먼트에 둘 이상의 경로 매개 변수를 정의할 수 있지만 리터럴 값으로 분리되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4038">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="57132-4039">예를 들어 `{controller=Home}{action=Index}`는 `{controller}` 및 `{action}` 사이에 리터럴 값이 없으므로 유효한 경로가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4039">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="57132-4040">이러한 경로 매개 변수는 이름이 있어야 하며 지정된 추가 특성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4040">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="57132-4041">경로 매개 변수 이외의 리터럴 텍스트(예: `{id}`) 및 경로 구분 기호(`/`)는 URL의 텍스트와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4041">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="57132-4042">텍스트 일치는 대/소문자를 구분하지 않으며 URL 경로의 디코딩된 표현을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4042">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="57132-4043">리터럴 경로 매개 변수 구분 기호(`{` 또는 `}`)와 일치시키려면 문자를 반복하여(`{{` 또는 `}}`) 구분 기호를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4043">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="57132-4044">선택적 파일 확장명이 있는 파일 이름을 캡처하려고 시도하는 URL 패턴에는 추가 고려 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4044">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="57132-4045">예를 들어 템플릿 `files/{filename}.{ext?}`를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4045">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="57132-4046">`filename` 및 `ext` 모두에 대한 값이 있으면 두 값이 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4046">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="57132-4047">URL에 `filename`에 대한 값만 있으면 후행 마침표(`.`)가 선택 사항이므로 경로가 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4047">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="57132-4048">다음 URL은 이 경로와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4048">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="57132-4049">별표(`*`)를 경로 매개 변수의 접두사로 사용하여 URI의 나머지 부분에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4049">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="57132-4050">이를 *범용* 매개 변수라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4050">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="57132-4051">예를 들어 `blog/{*slug}`는 `/blog`로 시작하고 모든 값(`slug` 경로 값에 할당된)이 뒤따르는 모든 URI와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4051">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="57132-4052">범용 매개 변수는 빈 문자열과 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4052">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="57132-4053">catch-all 매개 변수는 경로 구분 기호(`/`) 문자를 포함하여 URL을 생성하는 데 경로가 사용될 때 적절한 문자를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4053">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="57132-4054">예를 들어 경로 값이 `{ path = "my/path" }`인 경로 `foo/{*path}`는 `foo/my%2Fpath`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4054">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="57132-4055">이스케이프된 슬래시에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4055">Note the escaped forward slash.</span></span>

<span data-ttu-id="57132-4056">경로 매개 변수에는 등호(`=`)로 구분된 매개 변수 이름 뒤에 기본값을 지정하여 지정된 *기본값*이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4056">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="57132-4057">예를 들어 `{controller=Home}`은 `controller`에 대한 기본값으로 `Home`을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4057">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="57132-4058">URL에 매개 변수에 대한 값이 없는 경우 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4058">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="57132-4059">경로 매개 변수는 `id?`와 같이 매개 변수 이름의 끝에 물음표(`?`)를 추가하면 선택적이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4059">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="57132-4060">선택적 값과 기본 경로 매개 변수 간의 차이점은 기본값이 있는 경로 매개 변수는 항상 값을 생성한다는 것입니다. 선택적 매개 변수에는 요청 URL에서 값을 제공하는 경우에만 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4060">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="57132-4061">경로 매개 변수에는 URL에서 바인딩된 경로 값과 일치해야 한다는 제약 조건이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4061">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="57132-4062">경로 매개 변수 이름 뒤에 콜론(`:`)과 제약 조건 이름을 추가하여 경로 매개 변수에서 *인라인 제약 조건*을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4062">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="57132-4063">제약 조건에 인수가 필요한 경우 제약 조건 이름 뒤에 인수를 괄호(`(...)`)로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4063">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="57132-4064">또 다른 콜론(`:`) 및 제약 조건 이름을 추가하여 여러 인라인 제약 조건을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4064">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="57132-4065">제약 조건 이름 및 인수는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>의 인스턴스를 만드는 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 서비스로 전달되어 URL 처리에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4065">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="57132-4066">예를 들어 경로 템플릿 `blog/{article:minlength(10)}`는 인수 `10`으로 `minlength` 제약 조건을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4066">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="57132-4067">경로 제약 조건 및 프레임워크에서 제공하는 제약 조건 목록에 대한 자세한 내용은 [경로 제약 조건 참조](#route-constraint-reference) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4067">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="57132-4068">다음 표에서는 경로 템플릿 예제 및 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4068">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="57132-4069">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="57132-4069">Route Template</span></span>                           | <span data-ttu-id="57132-4070">URI 일치 예제</span><span class="sxs-lookup"><span data-stu-id="57132-4070">Example Matching URI</span></span>    | <span data-ttu-id="57132-4071">요청 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="57132-4071">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="57132-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4073">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4073">'Blazor'</span></span>
- <span data-ttu-id="57132-4074">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4074">'Identity'</span></span>
- <span data-ttu-id="57132-4075">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4075">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4076">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4076">'Razor'</span></span>
- <span data-ttu-id="57132-4077">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4077">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4079">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4079">'Blazor'</span></span>
- <span data-ttu-id="57132-4080">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4080">'Identity'</span></span>
- <span data-ttu-id="57132-4081">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4081">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4082">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4082">'Razor'</span></span>
- <span data-ttu-id="57132-4083">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4083">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4085">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4085">'Blazor'</span></span>
- <span data-ttu-id="57132-4086">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4086">'Identity'</span></span>
- <span data-ttu-id="57132-4087">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4087">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4088">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4088">'Razor'</span></span>
- <span data-ttu-id="57132-4089">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4089">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4091">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4091">'Blazor'</span></span>
- <span data-ttu-id="57132-4092">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4092">'Identity'</span></span>
- <span data-ttu-id="57132-4093">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4093">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4094">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4094">'Razor'</span></span>
- <span data-ttu-id="57132-4095">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4095">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4097">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4097">'Blazor'</span></span>
- <span data-ttu-id="57132-4098">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4098">'Identity'</span></span>
- <span data-ttu-id="57132-4099">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4099">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4100">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4100">'Razor'</span></span>
- <span data-ttu-id="57132-4101">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4101">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4103">'Blazor'</span></span>
- <span data-ttu-id="57132-4104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4104">'Identity'</span></span>
- <span data-ttu-id="57132-4105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4105">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4106">'Razor'</span></span>
- <span data-ttu-id="57132-4107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4107">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4109">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4109">'Blazor'</span></span>
- <span data-ttu-id="57132-4110">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4110">'Identity'</span></span>
- <span data-ttu-id="57132-4111">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4111">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4112">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4112">'Razor'</span></span>
- <span data-ttu-id="57132-4113">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4113">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4115">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4115">'Blazor'</span></span>
- <span data-ttu-id="57132-4116">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4116">'Identity'</span></span>
- <span data-ttu-id="57132-4117">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4117">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4118">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4118">'Razor'</span></span>
- <span data-ttu-id="57132-4119">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4119">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4121">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4121">'Blazor'</span></span>
- <span data-ttu-id="57132-4122">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4122">'Identity'</span></span>
- <span data-ttu-id="57132-4123">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4123">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4124">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4124">'Razor'</span></span>
- <span data-ttu-id="57132-4125">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4125">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4127">'Blazor'</span></span>
- <span data-ttu-id="57132-4128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4128">'Identity'</span></span>
- <span data-ttu-id="57132-4129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4129">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4130">'Razor'</span></span>
- <span data-ttu-id="57132-4131">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4133">'Blazor'</span></span>
- <span data-ttu-id="57132-4134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4134">'Identity'</span></span>
- <span data-ttu-id="57132-4135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4135">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4136">'Razor'</span></span>
- <span data-ttu-id="57132-4137">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4139">'Blazor'</span></span>
- <span data-ttu-id="57132-4140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4140">'Identity'</span></span>
- <span data-ttu-id="57132-4141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4141">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4142">'Razor'</span></span>
- <span data-ttu-id="57132-4143">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4145">'Blazor'</span></span>
- <span data-ttu-id="57132-4146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4146">'Identity'</span></span>
- <span data-ttu-id="57132-4147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4147">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4148">'Razor'</span></span>
- <span data-ttu-id="57132-4149">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4151">'Blazor'</span></span>
- <span data-ttu-id="57132-4152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4152">'Identity'</span></span>
- <span data-ttu-id="57132-4153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4153">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4154">'Razor'</span></span>
- <span data-ttu-id="57132-4155">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4157">'Blazor'</span></span>
- <span data-ttu-id="57132-4158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4158">'Identity'</span></span>
- <span data-ttu-id="57132-4159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4159">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4160">'Razor'</span></span>
- <span data-ttu-id="57132-4161">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4163">'Blazor'</span></span>
- <span data-ttu-id="57132-4164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4164">'Identity'</span></span>
- <span data-ttu-id="57132-4165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4165">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4166">'Razor'</span></span>
- <span data-ttu-id="57132-4167">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4169">'Blazor'</span></span>
- <span data-ttu-id="57132-4170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4170">'Identity'</span></span>
- <span data-ttu-id="57132-4171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4171">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4172">'Razor'</span></span>
- <span data-ttu-id="57132-4173">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4175">'Blazor'</span></span>
- <span data-ttu-id="57132-4176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4176">'Identity'</span></span>
- <span data-ttu-id="57132-4177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4177">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4178">'Razor'</span></span>
- <span data-ttu-id="57132-4179">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4179">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4181">'Blazor'</span></span>
- <span data-ttu-id="57132-4182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4182">'Identity'</span></span>
- <span data-ttu-id="57132-4183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4183">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4184">'Razor'</span></span>
- <span data-ttu-id="57132-4185">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4187">'Blazor'</span></span>
- <span data-ttu-id="57132-4188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4188">'Identity'</span></span>
- <span data-ttu-id="57132-4189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4189">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4190">'Razor'</span></span>
- <span data-ttu-id="57132-4191">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4193">'Blazor'</span></span>
- <span data-ttu-id="57132-4194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4194">'Identity'</span></span>
- <span data-ttu-id="57132-4195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4195">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4196">'Razor'</span></span>
- <span data-ttu-id="57132-4197">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4199">'Blazor'</span></span>
- <span data-ttu-id="57132-4200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4200">'Identity'</span></span>
- <span data-ttu-id="57132-4201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4201">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4202">'Razor'</span></span>
- <span data-ttu-id="57132-4203">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4205">'Blazor'</span></span>
- <span data-ttu-id="57132-4206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4206">'Identity'</span></span>
- <span data-ttu-id="57132-4207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4207">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4208">'Razor'</span></span>
- <span data-ttu-id="57132-4209">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4211">'Blazor'</span></span>
- <span data-ttu-id="57132-4212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4212">'Identity'</span></span>
- <span data-ttu-id="57132-4213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4213">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4214">'Razor'</span></span>
- <span data-ttu-id="57132-4215">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4217">'Blazor'</span></span>
- <span data-ttu-id="57132-4218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4218">'Identity'</span></span>
- <span data-ttu-id="57132-4219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4219">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4220">'Razor'</span></span>
- <span data-ttu-id="57132-4221">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4223">'Blazor'</span></span>
- <span data-ttu-id="57132-4224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4224">'Identity'</span></span>
- <span data-ttu-id="57132-4225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4225">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4226">'Razor'</span></span>
- <span data-ttu-id="57132-4227">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4229">'Blazor'</span></span>
- <span data-ttu-id="57132-4230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4230">'Identity'</span></span>
- <span data-ttu-id="57132-4231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4231">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4232">'Razor'</span></span>
- <span data-ttu-id="57132-4233">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4233">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4235">'Blazor'</span></span>
- <span data-ttu-id="57132-4236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4236">'Identity'</span></span>
- <span data-ttu-id="57132-4237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4237">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4238">'Razor'</span></span>
- <span data-ttu-id="57132-4239">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4241">'Blazor'</span></span>
- <span data-ttu-id="57132-4242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4242">'Identity'</span></span>
- <span data-ttu-id="57132-4243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4243">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4244">'Razor'</span></span>
- <span data-ttu-id="57132-4245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4247">'Blazor'</span></span>
- <span data-ttu-id="57132-4248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4248">'Identity'</span></span>
- <span data-ttu-id="57132-4249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4249">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4250">'Razor'</span></span>
- <span data-ttu-id="57132-4251">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4253">'Blazor'</span></span>
- <span data-ttu-id="57132-4254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4254">'Identity'</span></span>
- <span data-ttu-id="57132-4255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4255">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4256">'Razor'</span></span>
- <span data-ttu-id="57132-4257">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4259">'Blazor'</span></span>
- <span data-ttu-id="57132-4260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4260">'Identity'</span></span>
- <span data-ttu-id="57132-4261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4261">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4262">'Razor'</span></span>
- <span data-ttu-id="57132-4263">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4265">'Blazor'</span></span>
- <span data-ttu-id="57132-4266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4266">'Identity'</span></span>
- <span data-ttu-id="57132-4267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4267">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4268">'Razor'</span></span>
- <span data-ttu-id="57132-4269">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4271">'Blazor'</span></span>
- <span data-ttu-id="57132-4272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4272">'Identity'</span></span>
- <span data-ttu-id="57132-4273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4273">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4274">'Razor'</span></span>
- <span data-ttu-id="57132-4275">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4277">'Blazor'</span></span>
- <span data-ttu-id="57132-4278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4278">'Identity'</span></span>
- <span data-ttu-id="57132-4279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4279">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4280">'Razor'</span></span>
- <span data-ttu-id="57132-4281">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4283">'Blazor'</span></span>
- <span data-ttu-id="57132-4284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4284">'Identity'</span></span>
- <span data-ttu-id="57132-4285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4285">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4286">'Razor'</span></span>
- <span data-ttu-id="57132-4287">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4289">'Blazor'</span></span>
- <span data-ttu-id="57132-4290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4290">'Identity'</span></span>
- <span data-ttu-id="57132-4291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4291">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4292">'Razor'</span></span>
- <span data-ttu-id="57132-4293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4295">'Blazor'</span></span>
- <span data-ttu-id="57132-4296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4296">'Identity'</span></span>
- <span data-ttu-id="57132-4297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4297">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4298">'Razor'</span></span>
- <span data-ttu-id="57132-4299">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4301">'Blazor'</span></span>
- <span data-ttu-id="57132-4302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4302">'Identity'</span></span>
- <span data-ttu-id="57132-4303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4303">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4304">'Razor'</span></span>
- <span data-ttu-id="57132-4305">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4307">'Blazor'</span></span>
- <span data-ttu-id="57132-4308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4308">'Identity'</span></span>
- <span data-ttu-id="57132-4309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4309">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4310">'Razor'</span></span>
- <span data-ttu-id="57132-4311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4313">'Blazor'</span></span>
- <span data-ttu-id="57132-4314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4314">'Identity'</span></span>
- <span data-ttu-id="57132-4315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4315">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4316">'Razor'</span></span>
- <span data-ttu-id="57132-4317">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4319">'Blazor'</span></span>
- <span data-ttu-id="57132-4320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4320">'Identity'</span></span>
- <span data-ttu-id="57132-4321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4321">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4322">'Razor'</span></span>
- <span data-ttu-id="57132-4323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4325">'Blazor'</span></span>
- <span data-ttu-id="57132-4326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4326">'Identity'</span></span>
- <span data-ttu-id="57132-4327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4327">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4328">'Razor'</span></span>
- <span data-ttu-id="57132-4329">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4331">'Blazor'</span></span>
- <span data-ttu-id="57132-4332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4332">'Identity'</span></span>
- <span data-ttu-id="57132-4333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4333">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4334">'Razor'</span></span>
- <span data-ttu-id="57132-4335">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4337">'Blazor'</span></span>
- <span data-ttu-id="57132-4338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4338">'Identity'</span></span>
- <span data-ttu-id="57132-4339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4339">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4340">'Razor'</span></span>
- <span data-ttu-id="57132-4341">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4343">'Blazor'</span></span>
- <span data-ttu-id="57132-4344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4344">'Identity'</span></span>
- <span data-ttu-id="57132-4345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4345">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4346">'Razor'</span></span>
- <span data-ttu-id="57132-4347">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4349">'Blazor'</span></span>
- <span data-ttu-id="57132-4350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4350">'Identity'</span></span>
- <span data-ttu-id="57132-4351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4351">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4352">'Razor'</span></span>
- <span data-ttu-id="57132-4353">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4355">'Blazor'</span></span>
- <span data-ttu-id="57132-4356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4356">'Identity'</span></span>
- <span data-ttu-id="57132-4357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4357">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4358">'Razor'</span></span>
- <span data-ttu-id="57132-4359">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4361">'Blazor'</span></span>
- <span data-ttu-id="57132-4362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4362">'Identity'</span></span>
- <span data-ttu-id="57132-4363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4363">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4364">'Razor'</span></span>
- <span data-ttu-id="57132-4365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4367">'Blazor'</span></span>
- <span data-ttu-id="57132-4368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4368">'Identity'</span></span>
- <span data-ttu-id="57132-4369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4369">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4370">'Razor'</span></span>
- <span data-ttu-id="57132-4371">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4373">'Blazor'</span></span>
- <span data-ttu-id="57132-4374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4374">'Identity'</span></span>
- <span data-ttu-id="57132-4375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4375">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4376">'Razor'</span></span>
- <span data-ttu-id="57132-4377">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4379">'Blazor'</span></span>
- <span data-ttu-id="57132-4380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4380">'Identity'</span></span>
- <span data-ttu-id="57132-4381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4381">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4382">'Razor'</span></span>
- <span data-ttu-id="57132-4383">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4385">'Blazor'</span></span>
- <span data-ttu-id="57132-4386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4386">'Identity'</span></span>
- <span data-ttu-id="57132-4387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4387">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4388">'Razor'</span></span>
- <span data-ttu-id="57132-4389">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4391">'Blazor'</span></span>
- <span data-ttu-id="57132-4392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4392">'Identity'</span></span>
- <span data-ttu-id="57132-4393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4393">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4394">'Razor'</span></span>
- <span data-ttu-id="57132-4395">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4397">'Blazor'</span></span>
- <span data-ttu-id="57132-4398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4398">'Identity'</span></span>
- <span data-ttu-id="57132-4399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4399">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4400">'Razor'</span></span>
- <span data-ttu-id="57132-4401">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4403">'Blazor'</span></span>
- <span data-ttu-id="57132-4404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4404">'Identity'</span></span>
- <span data-ttu-id="57132-4405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4405">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4406">'Razor'</span></span>
- <span data-ttu-id="57132-4407">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4409">'Blazor'</span></span>
- <span data-ttu-id="57132-4410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4410">'Identity'</span></span>
- <span data-ttu-id="57132-4411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4411">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4412">'Razor'</span></span>
- <span data-ttu-id="57132-4413">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4415">'Blazor'</span></span>
- <span data-ttu-id="57132-4416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4416">'Identity'</span></span>
- <span data-ttu-id="57132-4417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4417">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4418">'Razor'</span></span>
- <span data-ttu-id="57132-4419">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4421">'Blazor'</span></span>
- <span data-ttu-id="57132-4422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4422">'Identity'</span></span>
- <span data-ttu-id="57132-4423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4423">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4424">'Razor'</span></span>
- <span data-ttu-id="57132-4425">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4427">'Blazor'</span></span>
- <span data-ttu-id="57132-4428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4428">'Identity'</span></span>
- <span data-ttu-id="57132-4429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4429">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4430">'Razor'</span></span>
- <span data-ttu-id="57132-4431">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4433">'Blazor'</span></span>
- <span data-ttu-id="57132-4434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4434">'Identity'</span></span>
- <span data-ttu-id="57132-4435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4435">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4436">'Razor'</span></span>
- <span data-ttu-id="57132-4437">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4439">'Blazor'</span></span>
- <span data-ttu-id="57132-4440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4440">'Identity'</span></span>
- <span data-ttu-id="57132-4441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4441">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4442">'Razor'</span></span>
- <span data-ttu-id="57132-4443">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4443">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4444">------------------------------------- | | `hello`                                  | `/hello`                | 단일 경로 `/hello`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4444">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="57132-4445">| | `{Page=Home}`                            | `/`                     | `Page`를 찾아 `Home`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4445">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="57132-4446">| | `{Page=Home}`                            | `/Contact`              | `Page`를 찾아 `Contact`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4446">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="57132-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` 컨트롤러 및 `List` 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="57132-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` 컨트롤러 및 `Details` 작업에 매핑합니다(`id`가 123으로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="57132-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="57132-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` 컨트롤러 및 `Index` 메서드에 매핑합니다(`id`가 무시됨).</span><span class="sxs-lookup"><span data-stu-id="57132-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="57132-4450">템플릿을 사용하는 것은 일반적으로 라우팅에 대한 가장 간단한 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4450">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="57132-4451">제약 조건 및 기본값을 경로 템플릿 외부에서 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4451">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="57132-4452">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4452">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="57132-4453">경로 제약 조건 참조</span><span class="sxs-lookup"><span data-stu-id="57132-4453">Route constraint reference</span></span>

<span data-ttu-id="57132-4454">경로 제약 조건은 들어오는 URL과 일치하고 URL 경로가 경로 값으로 토큰화되면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4454">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="57132-4455">일반적으로 경로 제약 조건은 경로 템플릿을 통해 연결된 경로 값을 검사하고 값 허용 여부에 대한 예/아니요 의사 결정을 내립니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4455">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="57132-4456">일부 경로 제약 조건은 경로 값 외부의 데이터를 사용하여 요청을 라우팅할 수 있는지 여부를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4456">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="57132-4457">예를 들어 <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint>는 해당 HTTP 동사에 따라 요청을 허용하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4457">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="57132-4458">제약 조건은 라우팅 요청 및 링크 생성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4458">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="57132-4459">제약 조건을 **입력 유효성 검사**에 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4459">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="57132-4460">제약 조건이 **입력 유효성 검사**에 사용되는 경우 잘못된 입력으로 인해 적절한 오류 메시지와 함께 *400 - 잘못된 요청* 대신 *404 - 찾을 수 없음* 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4460">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="57132-4461">경로 제약 조건은 특정 경로에 대한 입력의 유효성을 검사하는 것이 아니라 비슷한 경로를 **명확하게 구분하는 데** 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4461">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="57132-4462">다음 표에서는 경로 제약 조건 예제 및 예상되는 해당 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4462">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="57132-4463">제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-4463">constraint</span></span> | <span data-ttu-id="57132-4464">예제</span><span class="sxs-lookup"><span data-stu-id="57132-4464">Example</span></span> | <span data-ttu-id="57132-4465">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="57132-4465">Example Matches</span></span> | <span data-ttu-id="57132-4466">참고</span><span class="sxs-lookup"><span data-stu-id="57132-4466">Notes</span></span> |
| ---
<span data-ttu-id="57132-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4468">'Blazor'</span></span>
- <span data-ttu-id="57132-4469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4469">'Identity'</span></span>
- <span data-ttu-id="57132-4470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4470">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4471">'Razor'</span></span>
- <span data-ttu-id="57132-4472">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4474">'Blazor'</span></span>
- <span data-ttu-id="57132-4475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4475">'Identity'</span></span>
- <span data-ttu-id="57132-4476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4476">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4477">'Razor'</span></span>
- <span data-ttu-id="57132-4478">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4478">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4480">'Blazor'</span></span>
- <span data-ttu-id="57132-4481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4481">'Identity'</span></span>
- <span data-ttu-id="57132-4482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4482">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4483">'Razor'</span></span>
- <span data-ttu-id="57132-4484">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4484">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4486">'Blazor'</span></span>
- <span data-ttu-id="57132-4487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4487">'Identity'</span></span>
- <span data-ttu-id="57132-4488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4488">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4489">'Razor'</span></span>
- <span data-ttu-id="57132-4490">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4490">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4492">'Blazor'</span></span>
- <span data-ttu-id="57132-4493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4493">'Identity'</span></span>
- <span data-ttu-id="57132-4494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4494">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4495">'Razor'</span></span>
- <span data-ttu-id="57132-4496">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4496">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4498">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4498">'Blazor'</span></span>
- <span data-ttu-id="57132-4499">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4499">'Identity'</span></span>
- <span data-ttu-id="57132-4500">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4500">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4501">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4501">'Razor'</span></span>
- <span data-ttu-id="57132-4502">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4502">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4504">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4504">'Blazor'</span></span>
- <span data-ttu-id="57132-4505">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4505">'Identity'</span></span>
- <span data-ttu-id="57132-4506">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4506">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4507">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4507">'Razor'</span></span>
- <span data-ttu-id="57132-4508">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4508">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4510">'Blazor'</span></span>
- <span data-ttu-id="57132-4511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4511">'Identity'</span></span>
- <span data-ttu-id="57132-4512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4512">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4513">'Razor'</span></span>
- <span data-ttu-id="57132-4514">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4516">'Blazor'</span></span>
- <span data-ttu-id="57132-4517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4517">'Identity'</span></span>
- <span data-ttu-id="57132-4518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4518">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4519">'Razor'</span></span>
- <span data-ttu-id="57132-4520">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4520">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 임의의 정수와 일치합니다. | | `bool` | `{active:bool}` | `true`, `FALSE` | `true` 또는 `false`와 일치합니다(대/소문자 구분 안 함). | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 고정 문화권에서 유효한 `DateTime` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false` (case-insensitive) | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="57132-4522">위의 경고를 참조하세요.| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 고정 문화권에서 유효한 `decimal` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4522">See  preceding warning.| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="57132-4523">위의 경고를 참조하세요.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 고정 문화권에서 유효한 `double` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4523">See  preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="57132-4524">위의 경고를 참조하세요.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 고정 문화권에서 유효한 `float` 값과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4524">See  preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="57132-4525">위의 경고를 참조하세요.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 유효한 `Guid` 값을 찾습니다. | | `long` | `{ticks:long}` | `123456789`, `-123456789` | 유효한 `long` 값을 찾습니다. | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 문자열은 4자 이상이어야 합니다. | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 문자열은 8자 이상이어야 합니다. | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 문자열은 정확히 12자여야 합니다. | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 문자열은 8자 이상이고 16자 이하여야 합니다. | | `min(value)` | `{age:min(18)}` | `19` | 정수 값은 18 이상이어야 합니다. | | `max(value)` | `{age:max(120)}` | `91` | 정수 값은 120 이상이어야 합니다. | | `range(min,max)` | `{age:range(18,120)}` | `91` | 정수 값은 18 이상이고 120 이하여야 합니다.| | `alpha` | `{name:alpha}` | `Rick` | 문자열은 하나 이상의 영문자(`a`-`z`, 대/소문자 구분 안 함)로 구성되어야 합니다. | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 문자열은 정규식과 일치해야 합니다(정규식 정의에 대한 팁 참조). | | `required` | `{name:required}` | `Rick` | URL을 생성하는 동안 비-매개 변수 값이 존재하도록 강제하는 데 사용됩니다. |</span><span class="sxs-lookup"><span data-stu-id="57132-4525">See  preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive) | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression (see tips about defining a regular expression) | | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

<span data-ttu-id="57132-4526">콜론으로 구분된 여러 개의 제약 조건을 단일 매개 변수에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4526">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="57132-4527">예를 들어 다음 제약 조건은 매개 변수를 1 이상의 정수 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4527">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="57132-4528">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4528">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="57132-4529">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4529">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="57132-4530">프레임워크에서 제공한 경로 제약 조건은 경로 값에 저장된 값을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4530">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="57132-4531">URL에서 구문 분석되는 모든 경로 값은 문자열로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4531">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="57132-4532">예를 들어 `float` 제약 조건은 경로 값을 부동으로 변환하려고 하지만 변환된 값은 부동으로 변환될 수 있는지 확인하는 데만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4532">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="57132-4533">정규식</span><span class="sxs-lookup"><span data-stu-id="57132-4533">Regular expressions</span></span>

<span data-ttu-id="57132-4534">ASP.NET Core 프레임워크는 정규식 생성자에 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4534">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="57132-4535">이러한 멤버에 대한 설명은 <xref:System.Text.RegularExpressions.RegexOptions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4535">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="57132-4536">정규식은 라우팅 및 C# 언어에서 사용하는 것과 유사한 구분 기호 및 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4536">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="57132-4537">정규식 토큰은 이스케이프되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4537">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="57132-4538">라우팅에서 `^\d{3}-\d{2}-\d{4}$`라는 정규식을 사용하려면, C# 소스 파일에서는 `\` 문자열 이스케이프 문자를 이스케이프하기 위해서 식 문자열의 `\`(단일 백슬래시) 문자를 `\\`(이중 백슬래시) 문자로 제공해야 합니다([약어 문자열 리터럴](/dotnet/csharp/language-reference/keywords/string)을 사용하지 않는 한).</span><span class="sxs-lookup"><span data-stu-id="57132-4538">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="57132-4539">라우팅 매개 변수 구분 기호 문자(`{`, `}`, `[`, `]`)를 이스케이프하려면 식에서 해당 문자를 이중으로 사용합니다(`{{`, `}`, `[[`, `]]`).</span><span class="sxs-lookup"><span data-stu-id="57132-4539">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="57132-4540">다음 표는 정규식 및 이스케이프된 버전을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4540">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="57132-4541">정규식</span><span class="sxs-lookup"><span data-stu-id="57132-4541">Regular Expression</span></span>    | <span data-ttu-id="57132-4542">이스케이프된 정규식</span><span class="sxs-lookup"><span data-stu-id="57132-4542">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="57132-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4544">'Blazor'</span></span>
- <span data-ttu-id="57132-4545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4545">'Identity'</span></span>
- <span data-ttu-id="57132-4546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4546">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4547">'Razor'</span></span>
- <span data-ttu-id="57132-4548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4550">'Blazor'</span></span>
- <span data-ttu-id="57132-4551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4551">'Identity'</span></span>
- <span data-ttu-id="57132-4552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4552">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4553">'Razor'</span></span>
- <span data-ttu-id="57132-4554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4556">'Blazor'</span></span>
- <span data-ttu-id="57132-4557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4557">'Identity'</span></span>
- <span data-ttu-id="57132-4558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4558">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4559">'Razor'</span></span>
- <span data-ttu-id="57132-4560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4562">'Blazor'</span></span>
- <span data-ttu-id="57132-4563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4563">'Identity'</span></span>
- <span data-ttu-id="57132-4564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4564">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4565">'Razor'</span></span>
- <span data-ttu-id="57132-4566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4568">'Blazor'</span></span>
- <span data-ttu-id="57132-4569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4569">'Identity'</span></span>
- <span data-ttu-id="57132-4570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4570">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4571">'Razor'</span></span>
- <span data-ttu-id="57132-4572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4574">'Blazor'</span></span>
- <span data-ttu-id="57132-4575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4575">'Identity'</span></span>
- <span data-ttu-id="57132-4576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4576">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4577">'Razor'</span></span>
- <span data-ttu-id="57132-4578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4580">'Blazor'</span></span>
- <span data-ttu-id="57132-4581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4581">'Identity'</span></span>
- <span data-ttu-id="57132-4582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4582">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4583">'Razor'</span></span>
- <span data-ttu-id="57132-4584">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4586">'Blazor'</span></span>
- <span data-ttu-id="57132-4587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4587">'Identity'</span></span>
- <span data-ttu-id="57132-4588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4588">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4589">'Razor'</span></span>
- <span data-ttu-id="57132-4590">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4590">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4592">'Blazor'</span></span>
- <span data-ttu-id="57132-4593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4593">'Identity'</span></span>
- <span data-ttu-id="57132-4594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4594">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4595">'Razor'</span></span>
- <span data-ttu-id="57132-4596">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4598">'Blazor'</span></span>
- <span data-ttu-id="57132-4599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4599">'Identity'</span></span>
- <span data-ttu-id="57132-4600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4600">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4601">'Razor'</span></span>
- <span data-ttu-id="57132-4602">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4604">'Blazor'</span></span>
- <span data-ttu-id="57132-4605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4605">'Identity'</span></span>
- <span data-ttu-id="57132-4606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4606">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4607">'Razor'</span></span>
- <span data-ttu-id="57132-4608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4610">'Blazor'</span></span>
- <span data-ttu-id="57132-4611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4611">'Identity'</span></span>
- <span data-ttu-id="57132-4612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4612">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4613">'Razor'</span></span>
- <span data-ttu-id="57132-4614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4616">'Blazor'</span></span>
- <span data-ttu-id="57132-4617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4617">'Identity'</span></span>
- <span data-ttu-id="57132-4618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4618">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4619">'Razor'</span></span>
- <span data-ttu-id="57132-4620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4622">'Blazor'</span></span>
- <span data-ttu-id="57132-4623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4623">'Identity'</span></span>
- <span data-ttu-id="57132-4624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4624">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4625">'Razor'</span></span>
- <span data-ttu-id="57132-4626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4628">'Blazor'</span></span>
- <span data-ttu-id="57132-4629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4629">'Identity'</span></span>
- <span data-ttu-id="57132-4630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4630">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4631">'Razor'</span></span>
- <span data-ttu-id="57132-4632">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4634">'Blazor'</span></span>
- <span data-ttu-id="57132-4635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4635">'Identity'</span></span>
- <span data-ttu-id="57132-4636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4636">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4637">'Razor'</span></span>
- <span data-ttu-id="57132-4638">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4640">'Blazor'</span></span>
- <span data-ttu-id="57132-4641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4641">'Identity'</span></span>
- <span data-ttu-id="57132-4642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4642">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4643">'Razor'</span></span>
- <span data-ttu-id="57132-4644">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4646">'Blazor'</span></span>
- <span data-ttu-id="57132-4647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4647">'Identity'</span></span>
- <span data-ttu-id="57132-4648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4648">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4649">'Razor'</span></span>
- <span data-ttu-id="57132-4650">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4652">'Blazor'</span></span>
- <span data-ttu-id="57132-4653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4653">'Identity'</span></span>
- <span data-ttu-id="57132-4654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4654">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4655">'Razor'</span></span>
- <span data-ttu-id="57132-4656">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4658">'Blazor'</span></span>
- <span data-ttu-id="57132-4659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4659">'Identity'</span></span>
- <span data-ttu-id="57132-4660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4660">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4661">'Razor'</span></span>
- <span data-ttu-id="57132-4662">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4664">'Blazor'</span></span>
- <span data-ttu-id="57132-4665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4665">'Identity'</span></span>
- <span data-ttu-id="57132-4666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4666">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4667">'Razor'</span></span>
- <span data-ttu-id="57132-4668">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4668">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="57132-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="57132-4670">라우팅에 사용되는 정규식은 캐럿(`^`) 문자로 시작하고 문자열의 시작 위치와 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4670">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="57132-4671">식은 달러 기호(`$`) 문자로 끝나고 문자열의 끝과 일치하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4671">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="57132-4672">`^` 및 `$` 문자는 정규식이 전체 경로 매개 변수 값과 일치하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4672">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="57132-4673">`^` 및 `$` 문자가 없는 정규식은 문자열 내의 모든 하위 문자열과 일치하는데, 이는 종종 원하는 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4673">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="57132-4674">다음 표에서는 예제를 제공하고, 일치하거나 일치에 실패하는 이유를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4674">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="57132-4675">식</span><span class="sxs-lookup"><span data-stu-id="57132-4675">Expression</span></span>   | <span data-ttu-id="57132-4676">String</span><span class="sxs-lookup"><span data-stu-id="57132-4676">String</span></span>    | <span data-ttu-id="57132-4677">일치</span><span class="sxs-lookup"><span data-stu-id="57132-4677">Match</span></span> | <span data-ttu-id="57132-4678">주석</span><span class="sxs-lookup"><span data-stu-id="57132-4678">Comment</span></span>               |
| ---
<span data-ttu-id="57132-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4680">'Blazor'</span></span>
- <span data-ttu-id="57132-4681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4681">'Identity'</span></span>
- <span data-ttu-id="57132-4682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4682">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4683">'Razor'</span></span>
- <span data-ttu-id="57132-4684">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4686">'Blazor'</span></span>
- <span data-ttu-id="57132-4687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4687">'Identity'</span></span>
- <span data-ttu-id="57132-4688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4688">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4689">'Razor'</span></span>
- <span data-ttu-id="57132-4690">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4692">'Blazor'</span></span>
- <span data-ttu-id="57132-4693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4693">'Identity'</span></span>
- <span data-ttu-id="57132-4694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4694">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4695">'Razor'</span></span>
- <span data-ttu-id="57132-4696">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4698">'Blazor'</span></span>
- <span data-ttu-id="57132-4699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4699">'Identity'</span></span>
- <span data-ttu-id="57132-4700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4700">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4701">'Razor'</span></span>
- <span data-ttu-id="57132-4702">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4702">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4704">'Blazor'</span></span>
- <span data-ttu-id="57132-4705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4705">'Identity'</span></span>
- <span data-ttu-id="57132-4706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4706">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4707">'Razor'</span></span>
- <span data-ttu-id="57132-4708">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4710">'Blazor'</span></span>
- <span data-ttu-id="57132-4711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4711">'Identity'</span></span>
- <span data-ttu-id="57132-4712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4712">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4713">'Razor'</span></span>
- <span data-ttu-id="57132-4714">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4714">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4716">'Blazor'</span></span>
- <span data-ttu-id="57132-4717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4717">'Identity'</span></span>
- <span data-ttu-id="57132-4718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4718">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4719">'Razor'</span></span>
- <span data-ttu-id="57132-4720">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4722">'Blazor'</span></span>
- <span data-ttu-id="57132-4723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4723">'Identity'</span></span>
- <span data-ttu-id="57132-4724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4724">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4725">'Razor'</span></span>
- <span data-ttu-id="57132-4726">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4728">'Blazor'</span></span>
- <span data-ttu-id="57132-4729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4729">'Identity'</span></span>
- <span data-ttu-id="57132-4730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4730">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4731">'Razor'</span></span>
- <span data-ttu-id="57132-4732">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4734">'Blazor'</span></span>
- <span data-ttu-id="57132-4735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4735">'Identity'</span></span>
- <span data-ttu-id="57132-4736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4736">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4737">'Razor'</span></span>
- <span data-ttu-id="57132-4738">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4740">'Blazor'</span></span>
- <span data-ttu-id="57132-4741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4741">'Identity'</span></span>
- <span data-ttu-id="57132-4742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4742">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4743">'Razor'</span></span>
- <span data-ttu-id="57132-4744">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4746">'Blazor'</span></span>
- <span data-ttu-id="57132-4747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4747">'Identity'</span></span>
- <span data-ttu-id="57132-4748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4748">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4749">'Razor'</span></span>
- <span data-ttu-id="57132-4750">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4752">'Blazor'</span></span>
- <span data-ttu-id="57132-4753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4753">'Identity'</span></span>
- <span data-ttu-id="57132-4754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4754">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4755">'Razor'</span></span>
- <span data-ttu-id="57132-4756">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4758">'Blazor'</span></span>
- <span data-ttu-id="57132-4759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4759">'Identity'</span></span>
- <span data-ttu-id="57132-4760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4760">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4761">'Razor'</span></span>
- <span data-ttu-id="57132-4762">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4762">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4763">---------- | | `[a-z]{2}`   | hello     | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | 123abc456 | 예   | 부분 문자열 일치     | | `[a-z]{2}`   | mz        | 예   | 식을 찾습니다.    | | `[a-z]{2}`   | MZ        | 예   | 대/소문자를 구분하지 않습니다.    | | `^[a-z]{2}$` | hello     | 아니요    | 위의 `^` 및 `$`를 참조하세요. | | `^[a-z]{2}$` | 123abc456 | 아니요    | 위의 `^` 및 `$`를 참조하세요. |</span><span class="sxs-lookup"><span data-stu-id="57132-4763">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="57132-4764">정규식 구문에 대한 자세한 내용은 [.NET Framework 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4764">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="57132-4765">가능한 값의 알려진 집합으로 매개 변수를 제한하려면 정규식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4765">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="57132-4766">예를 들어 `{action:regex(^(list|get|create)$)}`는 `action` 경로 값을 `list`, `get` 또는 `create`으로만 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4766">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="57132-4767">제약 조건 사전으로 전달되면 `^(list|get|create)$` 문자열은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4767">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="57132-4768">알려진 제약 조건 중 하나와 일치하지 않는 제약 조건 사전(템플릿 내 인라인이 아님)에서 전달되는 제약 조건도 정규식으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4768">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="57132-4769">사용자 지정 경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="57132-4769">Custom Route Constraints</span></span>

<span data-ttu-id="57132-4770">기본 제공 경로 제약 조건 외에도 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스를 구현하여 사용자 지정 경로 제약 조건을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4770">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="57132-4771"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 인터페이스에는 제약 조건이 충족되는 경우 `true`를 반환하고 그렇지 않은 경우 `false`를 반환하는 `Match` 단일 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4771">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="57132-4772">사용자 지정 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>를 사용하려면 앱의 서비스 컨테이너에 있는 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>에 경로 제약 조건 형식을 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4772">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="57132-4773"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>은 경로 제약 조건 키를 해당 제약 조건의 유효성을 검사하는 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 구현과 매핑하는 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4773">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="57132-4774">`Startup.ConfigureServices`에서 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 호출의 일부로 또는 `services.Configure<RouteOptions>`를 사용하여 직접 <xref:Microsoft.AspNetCore.Routing.RouteOptions>를 구성하여 앱의 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>을 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4774">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="57132-4775">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="57132-4775">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="57132-4776">이제 제약 조건 형식을 등록할 때 지정한 이름을 사용하여 일반적인 방식으로 제약 조건을 경로에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4776">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="57132-4777">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="57132-4777">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="57132-4778">URL 생성 참조</span><span class="sxs-lookup"><span data-stu-id="57132-4778">URL generation reference</span></span>

<span data-ttu-id="57132-4779">다음 예제에서는 경로 값의 사전 및 <xref:Microsoft.AspNetCore.Routing.RouteCollection>이 지정된 경로에 대한 링크를 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4779">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="57132-4780">위의 샘플 끝부분에서 생성된 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>는 `/package/create/123`입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4780">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="57132-4781">사전은 "Track Package Route" 템플릿인 `package/{operation}/{id}`의 `operation` 및 `id` 경로 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4781">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="57132-4782">자세한 내용은 [라우팅 미들웨어 사용](#use-routing-middleware) 섹션의 샘플 코드 또는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-4782">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="57132-4783"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 생성자에 대한 두 번째 매개 변수는 *앰비언트 값*의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4783">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="57132-4784">개발자가 요청 컨텍스트 내에서 지정해야 하는 값의 수를 제한하므로 앰비언트 값은 사용하기 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4784">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="57132-4785">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4785">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="57132-4786">ASP.NET Core MVC 앱의 `HomeController`에 대한 `About` 작업에서는 `Index` 작업에 연결하기 위해 컨트롤러 경로 값을 지정할 필요가 없으며, `Home`이라는 앰비언트 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4786">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="57132-4787">매개 변수와 일치하지 않는 앰비언트 값은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4787">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="57132-4788">명시적으로 제공된 값이 앰비언트 값을 재정의하는 경우에도 앰비언트 값이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4788">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="57132-4789">URL에서 일치는 왼쪽에서 오른쪽으로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4789">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="57132-4790">명시적으로 제공되지만 경로의 세그먼트와 일치하지 않는 값은 쿼리 문자열에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4790">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="57132-4791">다음 표에서 경로 템플릿 `{controller}/{action}/{id?}`를 사용하는 경우 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="57132-4791">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="57132-4792">앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="57132-4792">Ambient Values</span></span>                     | <span data-ttu-id="57132-4793">명시적 값</span><span class="sxs-lookup"><span data-stu-id="57132-4793">Explicit Values</span></span>                        | <span data-ttu-id="57132-4794">결과</span><span class="sxs-lookup"><span data-stu-id="57132-4794">Result</span></span>                  |
| ---
<span data-ttu-id="57132-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4796">'Blazor'</span></span>
- <span data-ttu-id="57132-4797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4797">'Identity'</span></span>
- <span data-ttu-id="57132-4798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4798">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4799">'Razor'</span></span>
- <span data-ttu-id="57132-4800">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4802">'Blazor'</span></span>
- <span data-ttu-id="57132-4803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4803">'Identity'</span></span>
- <span data-ttu-id="57132-4804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4804">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4805">'Razor'</span></span>
- <span data-ttu-id="57132-4806">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4806">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4808">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4808">'Blazor'</span></span>
- <span data-ttu-id="57132-4809">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4809">'Identity'</span></span>
- <span data-ttu-id="57132-4810">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4810">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4811">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4811">'Razor'</span></span>
- <span data-ttu-id="57132-4812">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4812">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4814">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4814">'Blazor'</span></span>
- <span data-ttu-id="57132-4815">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4815">'Identity'</span></span>
- <span data-ttu-id="57132-4816">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4816">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4817">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4817">'Razor'</span></span>
- <span data-ttu-id="57132-4818">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4818">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4820">'Blazor'</span></span>
- <span data-ttu-id="57132-4821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4821">'Identity'</span></span>
- <span data-ttu-id="57132-4822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4822">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4823">'Razor'</span></span>
- <span data-ttu-id="57132-4824">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4826">'Blazor'</span></span>
- <span data-ttu-id="57132-4827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4827">'Identity'</span></span>
- <span data-ttu-id="57132-4828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4828">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4829">'Razor'</span></span>
- <span data-ttu-id="57132-4830">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4832">'Blazor'</span></span>
- <span data-ttu-id="57132-4833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4833">'Identity'</span></span>
- <span data-ttu-id="57132-4834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4834">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4835">'Razor'</span></span>
- <span data-ttu-id="57132-4836">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4838">'Blazor'</span></span>
- <span data-ttu-id="57132-4839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4839">'Identity'</span></span>
- <span data-ttu-id="57132-4840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4840">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4841">'Razor'</span></span>
- <span data-ttu-id="57132-4842">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4844">'Blazor'</span></span>
- <span data-ttu-id="57132-4845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4845">'Identity'</span></span>
- <span data-ttu-id="57132-4846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4846">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4847">'Razor'</span></span>
- <span data-ttu-id="57132-4848">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4850">'Blazor'</span></span>
- <span data-ttu-id="57132-4851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4851">'Identity'</span></span>
- <span data-ttu-id="57132-4852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4852">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4853">'Razor'</span></span>
- <span data-ttu-id="57132-4854">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4856">'Blazor'</span></span>
- <span data-ttu-id="57132-4857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4857">'Identity'</span></span>
- <span data-ttu-id="57132-4858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4858">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4859">'Razor'</span></span>
- <span data-ttu-id="57132-4860">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4862">'Blazor'</span></span>
- <span data-ttu-id="57132-4863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4863">'Identity'</span></span>
- <span data-ttu-id="57132-4864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4864">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4865">'Razor'</span></span>
- <span data-ttu-id="57132-4866">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4868">'Blazor'</span></span>
- <span data-ttu-id="57132-4869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4869">'Identity'</span></span>
- <span data-ttu-id="57132-4870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4870">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4871">'Razor'</span></span>
- <span data-ttu-id="57132-4872">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4874">'Blazor'</span></span>
- <span data-ttu-id="57132-4875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4875">'Identity'</span></span>
- <span data-ttu-id="57132-4876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4876">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4877">'Razor'</span></span>
- <span data-ttu-id="57132-4878">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4880">'Blazor'</span></span>
- <span data-ttu-id="57132-4881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4881">'Identity'</span></span>
- <span data-ttu-id="57132-4882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4882">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4883">'Razor'</span></span>
- <span data-ttu-id="57132-4884">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4884">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4886">'Blazor'</span></span>
- <span data-ttu-id="57132-4887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4887">'Identity'</span></span>
- <span data-ttu-id="57132-4888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4888">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4889">'Razor'</span></span>
- <span data-ttu-id="57132-4890">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4892">'Blazor'</span></span>
- <span data-ttu-id="57132-4893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4893">'Identity'</span></span>
- <span data-ttu-id="57132-4894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4894">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4895">'Razor'</span></span>
- <span data-ttu-id="57132-4896">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4898">'Blazor'</span></span>
- <span data-ttu-id="57132-4899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4899">'Identity'</span></span>
- <span data-ttu-id="57132-4900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4900">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4901">'Razor'</span></span>
- <span data-ttu-id="57132-4902">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4904">'Blazor'</span></span>
- <span data-ttu-id="57132-4905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4905">'Identity'</span></span>
- <span data-ttu-id="57132-4906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4906">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4907">'Razor'</span></span>
- <span data-ttu-id="57132-4908">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4908">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4910">'Blazor'</span></span>
- <span data-ttu-id="57132-4911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4911">'Identity'</span></span>
- <span data-ttu-id="57132-4912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4912">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4913">'Razor'</span></span>
- <span data-ttu-id="57132-4914">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4916">'Blazor'</span></span>
- <span data-ttu-id="57132-4917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4917">'Identity'</span></span>
- <span data-ttu-id="57132-4918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4918">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4919">'Razor'</span></span>
- <span data-ttu-id="57132-4920">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4922">'Blazor'</span></span>
- <span data-ttu-id="57132-4923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4923">'Identity'</span></span>
- <span data-ttu-id="57132-4924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4924">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4925">'Razor'</span></span>
- <span data-ttu-id="57132-4926">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4928">'Blazor'</span></span>
- <span data-ttu-id="57132-4929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4929">'Identity'</span></span>
- <span data-ttu-id="57132-4930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4930">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4931">'Razor'</span></span>
- <span data-ttu-id="57132-4932">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4934">'Blazor'</span></span>
- <span data-ttu-id="57132-4935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4935">'Identity'</span></span>
- <span data-ttu-id="57132-4936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4936">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4937">'Razor'</span></span>
- <span data-ttu-id="57132-4938">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4940">'Blazor'</span></span>
- <span data-ttu-id="57132-4941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4941">'Identity'</span></span>
- <span data-ttu-id="57132-4942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4942">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4943">'Razor'</span></span>
- <span data-ttu-id="57132-4944">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4946">'Blazor'</span></span>
- <span data-ttu-id="57132-4947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4947">'Identity'</span></span>
- <span data-ttu-id="57132-4948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4948">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4949">'Razor'</span></span>
- <span data-ttu-id="57132-4950">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4952">'Blazor'</span></span>
- <span data-ttu-id="57132-4953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4953">'Identity'</span></span>
- <span data-ttu-id="57132-4954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4954">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4955">'Razor'</span></span>
- <span data-ttu-id="57132-4956">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4958">'Blazor'</span></span>
- <span data-ttu-id="57132-4959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4959">'Identity'</span></span>
- <span data-ttu-id="57132-4960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4960">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4961">'Razor'</span></span>
- <span data-ttu-id="57132-4962">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4964">'Blazor'</span></span>
- <span data-ttu-id="57132-4965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4965">'Identity'</span></span>
- <span data-ttu-id="57132-4966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4966">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4967">'Razor'</span></span>
- <span data-ttu-id="57132-4968">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4970">'Blazor'</span></span>
- <span data-ttu-id="57132-4971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4971">'Identity'</span></span>
- <span data-ttu-id="57132-4972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4972">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4973">'Razor'</span></span>
- <span data-ttu-id="57132-4974">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4976">'Blazor'</span></span>
- <span data-ttu-id="57132-4977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4977">'Identity'</span></span>
- <span data-ttu-id="57132-4978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4978">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4979">'Razor'</span></span>
- <span data-ttu-id="57132-4980">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4980">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4982">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4982">'Blazor'</span></span>
- <span data-ttu-id="57132-4983">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4983">'Identity'</span></span>
- <span data-ttu-id="57132-4984">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4984">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4985">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4985">'Razor'</span></span>
- <span data-ttu-id="57132-4986">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4986">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4988">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4988">'Blazor'</span></span>
- <span data-ttu-id="57132-4989">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4989">'Identity'</span></span>
- <span data-ttu-id="57132-4990">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4990">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4991">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4991">'Razor'</span></span>
- <span data-ttu-id="57132-4992">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4992">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-4994">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-4994">'Blazor'</span></span>
- <span data-ttu-id="57132-4995">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-4995">'Identity'</span></span>
- <span data-ttu-id="57132-4996">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-4996">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-4997">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-4997">'Razor'</span></span>
- <span data-ttu-id="57132-4998">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-4998">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5000">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5000">'Blazor'</span></span>
- <span data-ttu-id="57132-5001">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5001">'Identity'</span></span>
- <span data-ttu-id="57132-5002">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5002">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5003">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5003">'Razor'</span></span>
- <span data-ttu-id="57132-5004">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5004">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5006">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5006">'Blazor'</span></span>
- <span data-ttu-id="57132-5007">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5007">'Identity'</span></span>
- <span data-ttu-id="57132-5008">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5008">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5009">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5009">'Razor'</span></span>
- <span data-ttu-id="57132-5010">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5010">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5012">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5012">'Blazor'</span></span>
- <span data-ttu-id="57132-5013">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5013">'Identity'</span></span>
- <span data-ttu-id="57132-5014">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5014">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5015">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5015">'Razor'</span></span>
- <span data-ttu-id="57132-5016">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5016">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5018">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5018">'Blazor'</span></span>
- <span data-ttu-id="57132-5019">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5019">'Identity'</span></span>
- <span data-ttu-id="57132-5020">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5020">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5021">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5021">'Razor'</span></span>
- <span data-ttu-id="57132-5022">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5022">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5024">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5024">'Blazor'</span></span>
- <span data-ttu-id="57132-5025">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5025">'Identity'</span></span>
- <span data-ttu-id="57132-5026">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5026">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5027">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5027">'Razor'</span></span>
- <span data-ttu-id="57132-5028">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5028">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5030">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5030">'Blazor'</span></span>
- <span data-ttu-id="57132-5031">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5031">'Identity'</span></span>
- <span data-ttu-id="57132-5032">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5032">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5033">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5033">'Razor'</span></span>
- <span data-ttu-id="57132-5034">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5034">'SignalR' uid:</span></span> 

-
<span data-ttu-id="57132-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="57132-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="57132-5036">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="57132-5036">'Blazor'</span></span>
- <span data-ttu-id="57132-5037">'Identity'</span><span class="sxs-lookup"><span data-stu-id="57132-5037">'Identity'</span></span>
- <span data-ttu-id="57132-5038">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="57132-5038">'Let's Encrypt'</span></span>
- <span data-ttu-id="57132-5039">'Razor'</span><span class="sxs-lookup"><span data-stu-id="57132-5039">'Razor'</span></span>
- <span data-ttu-id="57132-5040">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="57132-5040">'SignalR' uid:</span></span> 

<span data-ttu-id="57132-5041">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="57132-5041">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="57132-5042">경로에 매개 변수에 해당하지 않고 값이 명시적으로 제공된 기본값이 있는 경우 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-5042">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="57132-5043">`controller` 및 `action`에 대해 일치하는 값이 제공되는 경우에만 링크 생성에서 이 경로에 대한 링크를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-5043">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="57132-5044">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="57132-5044">Complex segments</span></span>

<span data-ttu-id="57132-5045">복잡한 세그먼트(예: `[Route("/x{token}y")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="57132-5045">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="57132-5046">복잡한 세그먼트 일치 방법에 대한 자세한 설명은 [이 코드](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="57132-5046">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="57132-5047">[코드 샘플](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)은 ASP.NET Core에서 사용되지 않지만 복잡한 세그먼트에 대한 적절한 설명을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="57132-5047">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>

::: moniker-end
