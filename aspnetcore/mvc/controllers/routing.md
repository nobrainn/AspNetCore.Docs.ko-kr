---
<span data-ttu-id="ca9d4-101">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-102">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-103">'Identity'</span></span>
- <span data-ttu-id="ca9d4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-105">'Razor'</span></span>
- <span data-ttu-id="ca9d4-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="ca9d4-107">ASP.NET Core의 컨트롤러 작업에 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="ca9d4-108">작성자: [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca9d4-109">ASP.NET Core 컨트롤러는 라우팅 [미들웨어](xref:fundamentals/middleware/index) 를 사용 하 여 들어오는 요청의 url을 일치 시키고이를 [작업](#action)에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="ca9d4-110">경로 템플릿:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-110">Routes templates:</span></span>

* <span data-ttu-id="ca9d4-111">는 시작 코드 또는 특성에서 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="ca9d4-112">URL 경로가 [작업과](#action)일치 하는 방식을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="ca9d4-113">는 링크에 대 한 Url을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="ca9d4-114">생성 된 링크는 일반적으로 응답에서 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="ca9d4-115">작업은 [일반적으로 라우트된 경로](#cr) 또는 [특성 라우팅](#ar)입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="ca9d4-116">컨트롤러 또는 [작업](#action) 에 경로를 배치 하면 특성이 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="ca9d4-117">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ca9d4-118">이 문서:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-118">This document:</span></span>

* <span data-ttu-id="ca9d4-119">MVC와 라우팅 간의 상호 작용에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="ca9d4-120">일반적인 MVC 앱이 라우팅 기능을 사용 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="ca9d4-121">모두 포함:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-121">Covers both:</span></span>
    * <span data-ttu-id="ca9d4-122">일반적으로 컨트롤러 및 뷰에 사용 되는 [라우팅](#cr) 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="ca9d4-123">REST Api와 함께 사용 되는 *특성 라우팅* 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="ca9d4-124">주로 REST Api에 대 한 라우팅에 관심이 있는 경우 [Rest api에 대 한 특성 라우팅](#ar) 섹션으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="ca9d4-125">라우팅 고급 라우팅에 대 한 자세한 내용은 [라우팅](xref:fundamentals/routing) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="ca9d4-126">ASP.NET Core 3.0에 추가 된 기본 라우팅 시스템 (끝점 라우팅 이라고 함)을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="ca9d4-127">호환성을 위해 이전 버전의 라우팅으로 컨트롤러를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="ca9d4-128">지침은 [2.2-3.0 마이그레이션 가이드](xref:migration/22-to-30) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="ca9d4-129">레거시 라우팅 시스템에 대 한 참조 자료는 [이 문서의 2.2 버전](xref:mvc/controllers/routing?view=aspnetcore-2.2) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="ca9d4-130">기본 경로 설정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-130">Set up conventional route</span></span>

<span data-ttu-id="ca9d4-131">`Startup.Configure`일반적으로 [기본 라우팅을](#crd)사용할 때 다음과 비슷한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="ca9d4-132">호출 내 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 는 단일 경로를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="ca9d4-133">단일 경로는 경로 라는 이름으로 지정 됩니다 `default` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-133">The single route is named `default` route.</span></span> <span data-ttu-id="ca9d4-134">컨트롤러 및 뷰를 사용 하는 대부분의 앱은 경로와 유사한 경로 템플릿을 사용 `default` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="ca9d4-135">REST Api는 [특성 라우팅을](#ar)사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="ca9d4-136">경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="ca9d4-137">URL 경로와 일치 합니다.`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="ca9d4-138">`{ controller = Products, action = Details, id = 5 }`경로를 토큰화 하 여 경로 값을 추출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ca9d4-139">경로 값을 추출 하면 앱에 라는 컨트롤러와 작업이 포함 된 경우 일치 하는 항목이 생성 됩니다 `ProductsController` `Details` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="ca9d4-140">`/Products/Details/5`모델은 `id = 5` 매개 변수를로 설정 하기 위해의 값을 바인딩합니다 `id` `5` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="ca9d4-141">자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="ca9d4-142">`{controller=Home}``Home`는를 기본값으로 정의 `controller` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="ca9d4-143">`{action=Index}``Index`는를 기본값으로 정의 `action` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="ca9d4-144">`?`의 문자는 `{id?}` 를 `id` 선택적으로 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="ca9d4-145">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ca9d4-146">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="ca9d4-147">URL 경로와 일치 `/` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="ca9d4-148">경로 값을 생성 `{ controller = Home, action = Index }` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="ca9d4-149">및에 대 한 값은 `controller` `action` 기본값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="ca9d4-150">`id`URL 경로에 해당 세그먼트가 없기 때문에에서 값을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ca9d4-151">`/`및 작업이 있는 경우에만 `HomeController` 일치 `Index` :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ca9d4-152">위의 컨트롤러 정의와 경로 템플릿을 사용 하 여 `HomeController.Index` 다음 URL 경로에 대해 작업을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="ca9d4-153">URL 경로는 `/` 경로 템플릿 기본 `Home` 컨트롤러 및 작업을 사용 합니다 `Index` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="ca9d4-154">URL 경로는 `/Home` 경로 템플릿 기본 작업을 사용 합니다 `Index` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="ca9d4-155">편의 메서드인 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="ca9d4-156">대체</span><span class="sxs-lookup"><span data-stu-id="ca9d4-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="ca9d4-157">라우팅은 및 미들웨어를 사용 하 여 구성 됩니다 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="ca9d4-158">컨트롤러를 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-158">To use controllers:</span></span>
>
> * <span data-ttu-id="ca9d4-159"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>안쪽 `UseEndpoints` 을 호출 하 여 라우트된 컨트롤러 [특성](#ar) 을 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="ca9d4-160"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>또는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 를 호출 하 여 [일반적으로 라우팅된](#cr) 컨트롤러를 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ca9d4-161">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-161">Conventional routing</span></span>

<span data-ttu-id="ca9d4-162">기존 라우팅은 컨트롤러 및 뷰에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="ca9d4-163">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="ca9d4-164">는 *규칙 기반 라우팅*의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="ca9d4-165">이는 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 *기존 라우팅* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ca9d4-166">첫 번째 경로 세그먼트는 `{controller=Home}` 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="ca9d4-167">두 번째 세그먼트 인은 `{action=Index}` [작업](#action) 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="ca9d4-168">세 번째 세그먼트는 `{id?}` 선택 사항에 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="ca9d4-169">`?`의를 `{id?}` 사용 하면이 옵션을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="ca9d4-170">`id`는 모델 엔터티에 매핑하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="ca9d4-171">이 경로를 사용 하 여 `default` URL 경로를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="ca9d4-172">`/Products/List`작업에 매핑됩니다 `ProductsController.List` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="ca9d4-173">`/Blog/Article/17`는에 매핑되고 `BlogController.Article` 일반적으로 `id` 매개 변수를 17에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="ca9d4-174">이 매핑:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-174">This mapping:</span></span>

* <span data-ttu-id="ca9d4-175">는 컨트롤러 및 [작업](#action) 이름만을 기반으로 **합니다.**</span><span class="sxs-lookup"><span data-stu-id="ca9d4-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="ca9d4-176">는 네임 스페이스, 소스 파일 위치 또는 메서드 매개 변수를 기반으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="ca9d4-177">기본 경로를 사용 하 여 기본 라우팅을 사용 하면 각 작업에 대 한 새 URL 패턴을 제공 하지 않고도 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="ca9d4-178">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 스타일 작업을 포함 하는 앱의 경우, 컨트롤러 간의 url에 대해 일관성을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="ca9d4-179">코드를 간소화 하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-179">Helps simplify the code.</span></span>
* <span data-ttu-id="ca9d4-180">UI를 보다 예측 가능 하 게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ca9d4-181">`id`위의 코드에서은 경로 템플릿에서 선택적으로 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="ca9d4-182">URL의 일부로 제공 되는 선택적 ID 없이 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="ca9d4-183">일반적으로 `id` URL에서이 생략 된 경우:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="ca9d4-184">`id`모델 바인딩에서로 설정 됩니다 `0` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="ca9d4-185">데이터베이스 일치에서 엔터티를 찾을 수 없습니다 `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="ca9d4-186">[특성 라우팅은](#ar) 사용자가 아닌 일부 작업에 필요한 ID를 설정 하는 세분화 된 제어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ca9d4-187">규칙에 따라 설명서에는 `id` 올바른 사용에 표시 될 수 있는 것과 같은 선택적 매개 변수가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="ca9d4-188">대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="ca9d4-189">기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="ca9d4-190">기본적이고 서술적인 라우팅 체계를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="ca9d4-191">UI 기반 앱에 대한 유용한 시작점입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="ca9d4-192">는 많은 웹 UI 앱에 필요한 유일한 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="ca9d4-193">더 큰 규모의 웹 UI 응용 프로그램의 경우에는 [영역](#areas) 을 사용 하는 다른 경로를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="ca9d4-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="ca9d4-195">호출 된 순서에 따라 해당 끝점에 **순서** 값을 자동으로 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="ca9d4-196">ASP.NET Core 3.0 이상의 엔드포인트 라우팅은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="ca9d4-197">경로 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="ca9d4-198">는 확장성 실행을 위한 순서 보증을 제공 하지 않으며 모든 끝점이 한 번에 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="ca9d4-199">[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="ca9d4-200">[특성 라우팅은](#ar) 이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="ca9d4-201">다중 기본 경로</span><span class="sxs-lookup"><span data-stu-id="ca9d4-201">Multiple conventional routes</span></span>

<span data-ttu-id="ca9d4-202">[conventional routes](#cr) `UseEndpoints` 및에 대 한 호출을 추가 하 여 내에서 여러 개의 기존 경로를 추가할 수 있습니다 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="ca9d4-203">이렇게 하면 여러 가지 규칙을 정의 하거나 다음과 같이 특정 [작업](#action)에 전용으로 사용 되는 기본 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="ca9d4-204">`blog`위의 코드에서 경로는 **전용 기본 경로**입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="ca9d4-205">다음 이유 때문에 전용 기본 경로 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="ca9d4-206">[기존 라우팅을](#cr)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="ca9d4-207">특정 [작업](#action)전용입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="ca9d4-208">`controller`및 `action` 는 경로 템플릿에 매개 변수로 표시 되지 않기 때문에 `"blog/{*article}"` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="ca9d4-209">기본값만 사용할 수 있습니다 `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="ca9d4-210">이 경로는 항상 작업에 매핑됩니다 `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ca9d4-211">`/Blog`, `/Blog/Article` 및 `/Blog/{any-string}` 은 블로그 경로와 일치 하는 유일한 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="ca9d4-212">위의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-212">The preceding example:</span></span>

* <span data-ttu-id="ca9d4-213">`blog`경로는 `default` 먼저 추가 되기 때문에 경로 보다 일치 하는 항목에 대 한 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="ca9d4-214">는 문서 이름을 URL의 일부로 포함 하는 것이 일반적으로 사용할 수 있는 [슬러그](https://developer.mozilla.org/docs/Glossary/Slug) 스타일 라우팅의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="ca9d4-215">ASP.NET Core 3.0 이상에서 라우팅은 다음과 같이 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="ca9d4-216">*경로*라는 개념을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-216">Define a concept called a *route*.</span></span> <span data-ttu-id="ca9d4-217">`UseRouting`은 경로 일치를 미들웨어 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="ca9d4-218">`UseRouting`미들웨어는 앱에 정의 된 끝점 집합을 살펴보고 요청에 따라 가장 적합 한 끝점 일치를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="ca9d4-219">또는과 같은 확장성의 실행 순서에 대 한 보장을 제공 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="ca9d4-220">라우팅에 대 한 참조 자료에 대 한 [라우팅](xref:fundamentals/routing) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="ca9d4-221">기본 라우팅 순서</span><span class="sxs-lookup"><span data-stu-id="ca9d4-221">Conventional routing order</span></span>

<span data-ttu-id="ca9d4-222">기존 라우팅은 앱에서 정의 된 작업과 컨트롤러의 조합만 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="ca9d4-223">이는 기존 경로가 중복 되는 경우를 단순화 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="ca9d4-224">, 및를 사용 하 여 경로를 추가 하면 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 호출 되는 순서에 따라 해당 끝점에 순서 값이 자동으로 할당 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="ca9d4-225">이전에 표시 된 경로의 일치 항목은 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="ca9d4-226">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca9d4-227">일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="ca9d4-228">와 같은 모든 경로 매개 변수를 사용 하는 [전용 기본 경로](#dcr) 를 사용 하면 `{*article}` 경로를 너무 많이 사용할 수 있습니다. 즉, 다른 경로와 일치 시키려는 url과 일치 하 게 됩니다. [greedy](xref:fundamentals/routing#greedy)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ca9d4-229">Greedy 일치를 방지 하려면 greedy 경로를 경로 테이블에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="ca9d4-230">모호한 작업 확인</span><span class="sxs-lookup"><span data-stu-id="ca9d4-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="ca9d4-231">두 끝점이 라우팅을 통해 일치 하는 경우 라우팅은 다음 중 하나를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="ca9d4-232">가장 적합 한 후보를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-232">Choose the best candidate.</span></span>
* <span data-ttu-id="ca9d4-233">예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-233">Throw an exception.</span></span>

<span data-ttu-id="ca9d4-234">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="ca9d4-235">위의 컨트롤러는 다음과 일치 하는 두 작업을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="ca9d4-236">URL 경로`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="ca9d4-237">데이터 `{ controller = Products33, action = Edit, id = 17 }` 를 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="ca9d4-238">MVC 컨트롤러의 일반적인 패턴은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="ca9d4-239">`Edit(int)`제품을 편집 하는 폼을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="ca9d4-240">`Edit(int, Product)`게시 된 폼을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="ca9d4-241">올바른 경로를 확인 하려면:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-241">To resolve the correct route:</span></span>

* <span data-ttu-id="ca9d4-242">`Edit(int, Product)`요청이 HTTP 인 경우이 선택 됩니다 `POST` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="ca9d4-243">`Edit(int)`[HTTP 동사가](#verb) 다른 항목이 면이 선택 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="ca9d4-244">`Edit(int)`는 일반적으로을 통해 호출 됩니다 `GET` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="ca9d4-245"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` 요청의 HTTP 메서드를 기반으로 하 여 선택할 수 있도록 라우팅에는가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="ca9d4-246">는 `HttpPostAttribute` `Edit(int, Product)` 보다 더 일치 하는 항목을 만듭니다 `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="ca9d4-247">와 같은 특성의 역할을 이해 하는 것이 중요 `HttpPostAttribute` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="ca9d4-248">다른 [HTTP 동사](#verb)에 대해 유사한 특성이 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="ca9d4-249">[기본 라우팅](#cr)에서 일반적으로 작업은 표시 양식, 제출 양식 워크플로의 일부인 경우에 동일한 동작 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="ca9d4-250">예를 들어 [두 개의 편집 작업 메서드 검사](xref:tutorials/first-mvc-app/controller-methods-views#get-post)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="ca9d4-251">라우팅이 최적의 후보를 선택할 수 없는 경우 일치 하는 <xref:System.Reflection.AmbiguousMatchException> 여러 끝점을 나열 하는이 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="ca9d4-252">기본 경로 이름</span><span class="sxs-lookup"><span data-stu-id="ca9d4-252">Conventional route names</span></span>

<span data-ttu-id="ca9d4-253">`"blog"` `"default"` 다음 예제에서 및 문자열은 기존 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-254">경로 이름은 경로에 논리적 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-254">The route names give the route a logical name.</span></span> <span data-ttu-id="ca9d4-255">명명 된 경로는 URL 생성에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="ca9d4-256">명명 된 경로를 사용 하면 경로 순서가 URL 생성을 복잡 하 게 만들 때 URL 만들기가 간단해 집니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ca9d4-257">경로 이름은 고유한 응용 프로그램 전체에 해당 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="ca9d4-258">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-258">Route names:</span></span>

* <span data-ttu-id="ca9d4-259">URL 일치 또는 요청 처리에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="ca9d4-260">URL 생성에만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-260">Are used only for URL generation.</span></span>

<span data-ttu-id="ca9d4-261">경로 이름 개념은 라우팅에서 [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="ca9d4-262">용어 **경로 이름** 및 **끝점 이름**:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="ca9d4-263">는 바꿔 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-263">Are interchangeable.</span></span>
* <span data-ttu-id="ca9d4-264">문서와 코드에 사용 되는 항목은 설명 된 API에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="ca9d4-265">REST Api에 대 한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="ca9d4-266">REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 [HTTP 동사로](#verb)나타내는 리소스 집합으로 모델링 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="ca9d4-267">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ca9d4-268">다음 `StartUp.Configure` 코드는 REST API에 일반적 이며 다음 샘플에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="ca9d4-269">위의 코드에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 는 `UseEndpoints` 특성 라우트된 컨트롤러를 매핑하기 위해 내부에서 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="ca9d4-270">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="ca9d4-270">In the following example:</span></span>

* <span data-ttu-id="ca9d4-271">위의 `Configure` 메서드가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="ca9d4-272">`HomeController`기본 기본 경로와 유사한 Url 집합과 일치 `{controller=Home}/{action=Index}/{id?}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="ca9d4-273">`HomeController.Index`작업은 URL 경로,, 또는에 대해 실행 `/` 됩니다 `/Home` `/Home/Index` `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="ca9d4-274">이 예에서는 특성 라우팅과 [기본 라우팅](#cr)간의 주요 프로그래밍 차이점을 강조 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="ca9d4-275">특성 라우팅은 경로를 지정 하는 데 더 많은 입력이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="ca9d4-276">기본 경로는 더 많은 간략하게 경로를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ca9d4-277">그러나 특성 라우팅은 각 [작업](#action)에 적용 되는 경로 템플릿을 정확 하 게 제어 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="ca9d4-278">다음 코드에서:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-278">In the following code:</span></span>

* <span data-ttu-id="ca9d4-279">컨트롤러 이름 및 작업 이름은 동작이 일치 하는 역할을 **하지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="ca9d4-280">이전 예제와 동일한 Url을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ca9d4-281">다음 코드에서는 및에 대 한 토큰 대체를 사용 합니다 `action` `controller` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="ca9d4-282">다음 코드는 `[Route("[controller]/[action]")]` 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ca9d4-283">위의 코드에서 `Index` 메서드 템플릿은 `/` 경로 템플릿에 또는 앞에와 야 합니다 `~/` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="ca9d4-284">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="ca9d4-285">경로 템플릿 선택에 대 한 자세한 내용은 [경로 템플릿 우선 순위](xref:fundamentals/routing#rtp) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="ca9d4-286">예약된 라우팅 이름</span><span class="sxs-lookup"><span data-stu-id="ca9d4-286">Reserved routing names</span></span>

<span data-ttu-id="ca9d4-287">다음 키워드는 컨트롤러 또는 페이지를 사용할 때 예약 된 경로 매개 변수 이름입니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="ca9d4-288">`page`특성 라우팅이 있는 경로 매개 변수로를 사용 하는 것은 일반적인 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="ca9d4-289">이렇게 하면 URL 생성 시 일관 되지 않은 동작이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="ca9d4-290">특수 매개 변수 이름은 url 생성 작업에서 페이지 또는 컨트롤러를 참조 하는지 확인 하는 데 사용 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="ca9d4-291">HTTP 동사 템플릿</span><span class="sxs-lookup"><span data-stu-id="ca9d4-291">HTTP verb templates</span></span>

<span data-ttu-id="ca9d4-292">ASP.NET Core에는 다음과 같은 HTTP 동사 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="ca9d4-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="ca9d4-294">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="ca9d4-295">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="ca9d4-296">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="ca9d4-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="ca9d4-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="ca9d4-299">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="ca9d4-299">Route templates</span></span>

<span data-ttu-id="ca9d4-300">ASP.NET Core에는 다음과 같은 경로 템플릿이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="ca9d4-301">모든 [HTTP 동사 템플릿은](#verb) 경로 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="ca9d4-302">[경로](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ca9d4-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="ca9d4-303">Http 동사 특성을 사용 하는 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="ca9d4-304">다음 컨트롤러를 살펴보십시오.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ca9d4-305">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="ca9d4-305">In the preceding code:</span></span>

* <span data-ttu-id="ca9d4-306">각 작업에는 `[HttpGet]` HTTP GET 요청에 대해서만 일치를 제한 하는 특성이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="ca9d4-307">`GetProduct`작업은 템플릿을 포함 `"{id}"` 하므로 `id` `"api/[controller]"` 컨트롤러의 템플릿에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="ca9d4-308">메서드 템플릿은 `"api/[controller]/"{id}""` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="ca9d4-309">따라서이 작업은,, 등에 대 한 GET 요청만 `/api/test2/xyz` 일치 `/api/test2/123` `/api/test2/{any string}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="ca9d4-310">작업에는 `GetIntProduct` 템플릿이 포함 되어 있습니다 `"int/{id:int}")` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="ca9d4-311">`:int`템플릿의 부분은 `id` 정수로 변환할 수 있는 문자열에 대 한 경로 값을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="ca9d4-312">GET 요청 대상 `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="ca9d4-313">이 작업과 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="ca9d4-314">404를 [찾을 수](https://developer.mozilla.org/docs/Web/HTTP/Status/404) 없음 오류를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="ca9d4-315">`GetInt2Product`작업은 `{id}` 템플릿에 포함 되지만 `id` 정수로 변환할 수 있는 값으로 제한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="ca9d4-316">GET 요청 대상 `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="ca9d4-317">이 경로와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-317">Matches this route.</span></span>
  * <span data-ttu-id="ca9d4-318">모델 바인딩이 정수로 변환 되지 않습니다 `abc` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="ca9d4-319">`id`메서드의 매개 변수는 정수입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="ca9d4-320">모델 바인딩이 정수로 변환 하지 못해 [400 잘못 된 요청](https://developer.mozilla.org/docs/Web/HTTP/Status/400) 을 반환 합니다 `abc` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="ca9d4-321">특성 라우팅은 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> , 및와 같은 특성을 사용할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="ca9d4-322">모든 [HTTP 동사](#verb) 특성은 경로 템플릿을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="ca9d4-323">다음 예에서는 동일한 경로 템플릿과 일치 하는 두 가지 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="ca9d4-324">URL 경로 사용 `/products3` :</span><span class="sxs-lookup"><span data-stu-id="ca9d4-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="ca9d4-325">`MyProductsController.ListProducts` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `GET` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="ca9d4-326">`MyProductsController.CreateProduct` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `POST` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="ca9d4-327">REST API를 빌드할 때 `[Route(...)]` 작업에서 모든 HTTP 메서드를 허용 하므로 작업 메서드에서를 사용 해야 하는 경우는 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="ca9d4-328">API에서 지 원하는 항목을 정확 하 게 이해 하려면 보다 구체적인 [HTTP 동사 특성](#verb) 을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="ca9d4-329">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ca9d4-330">REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 HTTP 동사로 나타내는 리소스 집합으로 모델링 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="ca9d4-331">즉, 동일한 논리 리소스에 대 한 GET 및 POST와 같은 많은 작업은 동일한 URL을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="ca9d4-332">특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="ca9d4-333">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ca9d4-334">다음 예제에서 `id` 는 URL 경로의 일부로 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ca9d4-335">`Products2ApiController.GetProduct(int)`작업은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="ca9d4-336">다음과 같이 URL 경로를 사용 하 여 실행 됩니다.`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="ca9d4-337">URL 경로를 사용 하 여 실행 되지 않습니다 `/products2` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="ca9d4-338">[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="ca9d4-339">자세한 내용은 [소비 특성을 사용 하 여 지원 되는 요청 콘텐츠 형식 정의](xref:web-api/index#consumes)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="ca9d4-340">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="ca9d4-341">에 대 한 자세한 내용은 `[ApiController]` [ApiController 특성](xref:web-api/index##apicontroller-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="ca9d4-342">경로 이름</span><span class="sxs-lookup"><span data-stu-id="ca9d4-342">Route name</span></span>

<span data-ttu-id="ca9d4-343">다음 코드는 의 `Products_List`경로 이름을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ca9d4-344">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ca9d4-345">경로 이름:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-345">Route names:</span></span>

* <span data-ttu-id="ca9d4-346">라우팅의 URL 일치 동작에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="ca9d4-347">URL 생성에만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-347">Are only used for URL generation.</span></span>

<span data-ttu-id="ca9d4-348">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ca9d4-349">앞의 코드와 `id` 매개 변수를 선택적 ()으로 정의 하는 기존의 기본 경로를 대조 합니다 `{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ca9d4-350">Api를 정확 하 게 지정 하는 기능에는 `/products` 및 `/products/5` 를 다른 작업에 디스패치할 수 있는 것과 같은 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="ca9d4-351">특성 경로 조합</span><span class="sxs-lookup"><span data-stu-id="ca9d4-351">Combining attribute routes</span></span>

<span data-ttu-id="ca9d4-352">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ca9d4-353">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ca9d4-354">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="ca9d4-355">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-355">In the preceding example:</span></span>

* <span data-ttu-id="ca9d4-356">URL 경로가 `/products` 일치 하는 경우`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="ca9d4-357">URL 경로는 `/products/5` 와 일치할 수 있습니다 `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="ca9d4-358">이러한 작업은 모두 `GET` 특성으로 표시 되어 있으므로 HTTP만 일치 `[HttpGet]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="ca9d4-359">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ca9d4-360">다음 예에서는 기본 경로와 유사한 URL 경로 집합을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="ca9d4-361">다음 표에서는 `[Route]` 위의 코드에 있는 특성에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="ca9d4-362">attribute</span><span class="sxs-lookup"><span data-stu-id="ca9d4-362">Attribute</span></span>               | <span data-ttu-id="ca9d4-363">와 결합`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="ca9d4-364">경로 템플릿을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-364">Defines route template</span></span> |
| ---
<span data-ttu-id="ca9d4-365">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-366">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-367">'Identity'</span></span>
- <span data-ttu-id="ca9d4-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-369">'Razor'</span></span>
- <span data-ttu-id="ca9d4-370">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-371">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-372">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-373">'Identity'</span></span>
- <span data-ttu-id="ca9d4-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-375">'Razor'</span></span>
- <span data-ttu-id="ca9d4-376">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-377">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-378">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-379">'Identity'</span></span>
- <span data-ttu-id="ca9d4-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-381">'Razor'</span></span>
- <span data-ttu-id="ca9d4-382">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-383">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-384">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-385">'Identity'</span></span>
- <span data-ttu-id="ca9d4-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-387">'Razor'</span></span>
- <span data-ttu-id="ca9d4-388">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-389">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-390">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-391">'Identity'</span></span>
- <span data-ttu-id="ca9d4-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-393">'Razor'</span></span>
- <span data-ttu-id="ca9d4-394">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-395">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-396">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-397">'Identity'</span></span>
- <span data-ttu-id="ca9d4-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-399">'Razor'</span></span>
- <span data-ttu-id="ca9d4-400">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-400">'SignalR' uid:</span></span> 

<span data-ttu-id="ca9d4-401">--------- | ---제목: author: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-402">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-403">'Identity'</span></span>
- <span data-ttu-id="ca9d4-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-405">'Razor'</span></span>
- <span data-ttu-id="ca9d4-406">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-407">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-408">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-409">'Identity'</span></span>
- <span data-ttu-id="ca9d4-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-411">'Razor'</span></span>
- <span data-ttu-id="ca9d4-412">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-413">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-414">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-415">'Identity'</span></span>
- <span data-ttu-id="ca9d4-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-417">'Razor'</span></span>
- <span data-ttu-id="ca9d4-418">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-419">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-420">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-421">'Identity'</span></span>
- <span data-ttu-id="ca9d4-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-423">'Razor'</span></span>
- <span data-ttu-id="ca9d4-424">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-424">'SignalR' uid:</span></span> 

<span data-ttu-id="ca9d4-425">------ | ---제목: author: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-426">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-427">'Identity'</span></span>
- <span data-ttu-id="ca9d4-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-429">'Razor'</span></span>
- <span data-ttu-id="ca9d4-430">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca9d4-431">제목: 작성자: 설명: ms author: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca9d4-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-432">'Blazor'</span></span>
- <span data-ttu-id="ca9d4-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-433">'Identity'</span></span>
- <span data-ttu-id="ca9d4-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca9d4-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca9d4-435">'Razor'</span></span>
- <span data-ttu-id="ca9d4-436">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-436">'SignalR' uid:</span></span> 

<span data-ttu-id="ca9d4-437">----- | | `[Route("")]` | 예 | `"Home"` |
| `[Route("Index")]` | 예 | `"Home/Index"` |
| `[Route("/")]` | **아니요** | `""` |
 | `[Route("About")]` | 예 | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="ca9d4-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="ca9d4-438">특성 경로 순서</span><span class="sxs-lookup"><span data-stu-id="ca9d4-438">Attribute route order</span></span>

<span data-ttu-id="ca9d4-439">라우팅은 트리를 빌드하고 모든 끝점을 동시에 일치 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="ca9d4-440">경로 항목은 이상적인 순서로 배치 된 것 처럼 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="ca9d4-441">가장 구체적인 경로는 보다 일반적인 경로 보다 먼저 실행 될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ca9d4-442">예를 들어와 같은 특성 경로는 `blog/search/{topic}` 와 같은 특성 경로 보다 구체적입니다 `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="ca9d4-443">`blog/search/{topic}`경로는 보다 구체적 이므로 기본적으로 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="ca9d4-444">개발자는 [기본 라우팅을](#cr)사용 하 여 원하는 순서로 경로를 배치할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ca9d4-445">특성 경로는 속성을 사용 하 여 주문을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="ca9d4-446">제공 된 모든 프레임 워크 [경로 특성](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 에는가 포함 `Order` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="ca9d4-447">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ca9d4-448">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-448">The default order is `0`.</span></span> <span data-ttu-id="ca9d4-449">를 사용 하 여 경로를 설정 하 `Order = -1` 는 것은 순서를 설정 하지 않은 경로 보다 먼저 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="ca9d4-450">기본 경로 순서 지정 후를 사용 하 여 경로를 설정 `Order = 1` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="ca9d4-451">**Avoid** 에 따라 방지 `Order` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="ca9d4-452">응용 프로그램의 URL 공간에 올바른 경로를 위해 명시적인 순서 값이 필요한 경우에는 클라이언트와 혼동 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ca9d4-453">일반적으로 특성 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ca9d4-454">URL 생성에 사용 되는 기본 순서가 작동 하지 않는 경우 경로 이름을 재정의로 사용 하는 것은 일반적으로 속성을 적용 하는 것 보다 간단 합니다 `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ca9d4-455">경로 일치를 정의 하는 다음 두 개의 컨트롤러를 고려 하십시오 `/home` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ca9d4-456">위의 코드를 요청 하면 `/home` 다음과 유사한 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="ca9d4-457">`Order`경로 특성 중 하나에를 추가 하면 모호성을 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="ca9d4-458">위의 코드를 사용 하 여 `/home` 끝점을 실행 `HomeController.Index` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="ca9d4-459">에 가져오려면를 `MyDemoController.MyIndex` 요청 `/home/MyIndex` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="ca9d4-460">**고**:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-460">**Note**:</span></span>

* <span data-ttu-id="ca9d4-461">위의 코드는 예 이거나 낮은 라우팅 디자인입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="ca9d4-462">속성을 설명 하는 데 사용 되었습니다 `Order` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="ca9d4-463">`Order`속성은 모호성을 해결 하 고 해당 템플릿은 일치 시킬 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="ca9d4-464">템플릿을 제거 하는 것이 좋습니다 `[Route("Home")]` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="ca9d4-465">페이지 [ Razor 경로 및 앱 규칙:](xref:razor-pages/razor-pages-conventions#route-order) 경로 순서와 페이지의 경로 순서에 대 한 정보를 참조 하세요 Razor .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="ca9d4-466">일부 경우에는 모호한 경로를 사용 하 여 HTTP 500 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="ca9d4-467">[로깅을](xref:fundamentals/logging/index) 사용 하 여를 일으킨 끝점을 확인 `AmbiguousMatchException` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ca9d4-468">경로 템플릿에서 토큰 바꾸기 [컨트롤러], [작업], [영역]</span><span class="sxs-lookup"><span data-stu-id="ca9d4-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="ca9d4-469">편의를 위해 특성 경로는 토큰을 다음 중 하나로 묶어 예약 된 경로 매개 변수에 대 한 토큰 바꾸기를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="ca9d4-470">대괄호:`[]`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="ca9d4-471">중괄호:`{}`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-471">Curly braces: `{}`</span></span>

<span data-ttu-id="ca9d4-472">`[action]`, 및 토큰은 `[area]` `[controller]` 경로가 정의 된 작업에서 동작 이름, 영역 이름 및 컨트롤러 이름 값으로 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ca9d4-473">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="ca9d4-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="ca9d4-474">항목`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="ca9d4-475">항목`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="ca9d4-476">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ca9d4-477">앞의 예제는 다음 코드와 동일 하 게 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="ca9d4-478">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ca9d4-479">토큰 교체와 강력 하 게 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="ca9d4-480">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="ca9d4-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대해 고유한 경로 이름을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="ca9d4-482">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="ca9d4-483">는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="ca9d4-484">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ca9d4-485">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ca9d4-486">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ca9d4-487">매개 변수 변환기는 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer>를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="ca9d4-488">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을로 변경 합니다 `subscription-management` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="ca9d4-489"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="ca9d4-490">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ca9d4-491">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="ca9d4-492">이전 `ListAll` 메서드는와 일치 `/subscription-management/list-all` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="ca9d4-493">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="ca9d4-494">슬러그의 정의는 [슬러그의 MDN 웹 문서](https://developer.mozilla.org/docs/Glossary/Slug) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="ca9d4-495">여러 특성 경로</span><span class="sxs-lookup"><span data-stu-id="ca9d4-495">Multiple attribute routes</span></span>

<span data-ttu-id="ca9d4-496">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ca9d4-497">가장 일반적인 사용 방법은 다음 예제와 같이 기본 규칙 기반 경로의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="ca9d4-498">여러 경로 특성을 컨트롤러에 배치 하면 각 경로 특성이 동작 메서드의 각 경로 특성과 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="ca9d4-499">모든 [HTTP 동사](#verb) 경로 제약 조건은를 구현 `IActionConstraint` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="ca9d4-500">을 구현 하는 여러 경로 특성이 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 동작에 배치 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="ca9d4-501">각 작업 제약 조건은 컨트롤러에 적용 된 경로 템플릿과 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="ca9d4-502">작업에 여러 경로를 사용 하면 유용 하 고 강력 하 게 보일 수 있습니다. 앱의 URL 공간을 기본 및 잘 정의 된 상태로 유지 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="ca9d4-503">기존 클라이언트를 지 원하는 경우와 같이 필요한 경우에 **만** 작업에 대해 여러 경로를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ca9d4-504">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ca9d4-505">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="ca9d4-506">위의 코드에서는 `[HttpPost("product/{id:int}")]` 경로 제약 조건을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="ca9d4-507">`ProductsController.ShowProduct`작업은와 같은 URL 경로만 일치 시킵니다 `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="ca9d4-508">경로 템플릿 부분은 `{id:int}` 해당 세그먼트를 정수로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ca9d4-509">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ca9d4-510">IRouteTemplateProvider를 사용 하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="ca9d4-511">모든 [경로 특성](#rt) 은를 구현 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="ca9d4-512">ASP.NET Core 런타임:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="ca9d4-513">응용 프로그램이 시작 될 때 컨트롤러 클래스 및 작업 메서드에서 특성을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="ca9d4-514">는를 구현 하는 특성을 사용 하 여 `IRouteTemplateProvider` 초기 경로 집합을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ca9d4-515">`IRouteTemplateProvider`을 구현 하 여 사용자 지정 경로 특성을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="ca9d4-516">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="ca9d4-517">이전 메서드는를 `Get` 반환 합니다 `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ca9d4-518">응용 프로그램 모델을 사용 하 여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="ca9d4-519">응용 프로그램 모델:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-519">The application model:</span></span>

* <span data-ttu-id="ca9d4-520">는 시작할 때 생성 되는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="ca9d4-521">앱에서 작업을 라우팅하고 실행 하기 위해 ASP.NET Core에서 사용 하는 모든 메타 데이터를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="ca9d4-522">응용 프로그램 모델은 경로 특성에서 수집 된 모든 데이터를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="ca9d4-523">경로 특성의 데이터는 구현에 의해 제공 됩니다 `IRouteTemplateProvider` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="ca9d4-524">규약</span><span class="sxs-lookup"><span data-stu-id="ca9d4-524">Conventions:</span></span>

* <span data-ttu-id="ca9d4-525">응용 프로그램 모델을 수정 하 여 라우팅이 동작 하는 방식을 사용자 지정 하도록를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="ca9d4-526">앱 시작 시 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-526">Are read at app startup.</span></span>

<span data-ttu-id="ca9d4-527">이 섹션에서는 응용 프로그램 모델을 사용 하 여 라우팅을 사용자 지정 하는 기본적인 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="ca9d4-528">다음 코드는 프로젝트의 폴더 구조를 사용 하 여 경로를 대략적으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="ca9d4-529">다음 코드는 `namespace` 특성이 라우팅되는 컨트롤러에 규칙이 적용 되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="ca9d4-530">예를 들어 다음 컨트롤러는를 사용 하지 않습니다 `NamespaceRoutingConvention` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ca9d4-531">`NamespaceRoutingConvention.Apply` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="ca9d4-532">컨트롤러에서 특성을 라우팅하는 경우 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="ca9d4-533">을 기반으로 하는 컨트롤러 템플릿을 설정 합니다 `namespace` . 이때 기본가 `namespace` 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="ca9d4-534">는 `NamespaceRoutingConvention` 다음에 적용할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="ca9d4-535">예를 들어 다음 컨트롤러를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="ca9d4-536">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="ca9d4-536">In the preceding code:</span></span>

* <span data-ttu-id="ca9d4-537">밑 `namespace` 이 인 `My.Application` 경우</span><span class="sxs-lookup"><span data-stu-id="ca9d4-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="ca9d4-538">이전 컨트롤러의 전체 이름은 `My.Application.Admin.Controllers.UsersController` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="ca9d4-539">는 `NamespaceRoutingConvention` 컨트롤러 템플릿을로 설정 합니다 `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="ca9d4-540">를 `NamespaceRoutingConvention` 컨트롤러의 특성으로 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ca9d4-541">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ca9d4-542">ASP.NET Core apps는 기존 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ca9d4-543">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ca9d4-544">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca9d4-545">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca9d4-546">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ca9d4-547">컨트롤러의 **모든** 경로 특성은 컨트롤러 특성의 모든 작업 **을 라우팅합니다.**</span><span class="sxs-lookup"><span data-stu-id="ca9d4-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="ca9d4-548">특성 라우팅 및 기존 라우팅은 동일한 라우팅 엔진을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="ca9d4-549">URL 생성 및 앰비언트 값</span><span class="sxs-lookup"><span data-stu-id="ca9d4-549">URL Generation and ambient values</span></span>

<span data-ttu-id="ca9d4-550">앱은 라우팅 URL 생성 기능을 사용 하 여 작업에 대 한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ca9d4-551">Url을 생성 하면 하드 코딩 되는 Url이 제거 되어 코드가 더 강력 하 고 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="ca9d4-552">이 섹션에서는 MVC에서 제공 하는 URL 생성 기능에 초점을 맞춘 후 URL 생성이 작동 하는 방식에 대 한 기본 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="ca9d4-553">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ca9d4-554"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>인터페이스는 MVC와 URL 생성을 위한 라우팅 간의 기본 인프라 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ca9d4-555">인스턴스는 `IUrlHelper` `Url` 컨트롤러, 뷰 및 뷰 구성 요소의 속성을 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ca9d4-556">다음 예제에서 `IUrlHelper` 인터페이스는 속성을 통해 `Controller.Url` 다른 동작에 대 한 URL을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-557">앱에서 기본 기본 경로를 사용 하는 경우 변수의 값은 `url` URL 경로 문자열입니다 `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ca9d4-558">이 URL 경로는 다음을 결합 하 여 라우팅하는 방식으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="ca9d4-559">**앰비언트 값**이라고 하는 현재 요청의 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="ca9d4-560">에 전달 되는 값으로 `Url.Action` , 해당 값을 경로 템플릿으로 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ca9d4-561">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ca9d4-562">값이 없는 경로 매개 변수는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="ca9d4-563">기본값이 있는 경우 기본값을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="ca9d4-564">선택 사항인 경우 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-564">Be skipped if it's optional.</span></span> <span data-ttu-id="ca9d4-565">예를 들어 `id` 경로 템플릿의 `{controller}/{action}/{id?}` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="ca9d4-566">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ca9d4-567">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ca9d4-568">위의 예에서는 `Url.Action` [기존 라우팅을](#cr)가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="ca9d4-569">개념은 서로 다르지만 URL 생성은 [특성 라우팅과](#ar)유사 하 게 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="ca9d4-570">기본 라우팅을 사용 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-570">With conventional routing:</span></span>

* <span data-ttu-id="ca9d4-571">경로 값은 템플릿을 확장 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="ca9d4-572">및에 대 한 경로 값은 `controller` `action` 일반적으로 해당 템플릿에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="ca9d4-573">이는 라우팅을 통해 일치 하는 Url이 규칙을 준수 하기 때문에 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="ca9d4-574">다음 예에서는 특성 라우팅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-575">`Source`위의 코드에서 작업은을 생성 `custom/url/to/destination` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="ca9d4-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>는의 대 안으로 ASP.NET Core 3.0에 추가 되었습니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="ca9d4-577">`LinkGenerator`는 유사 하지만 유연한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="ca9d4-578">의 각 메서드에 `IUrlHelper` 는의 해당 메서드 패밀리가 있습니다 `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ca9d4-579">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-579">Generating URLs by action name</span></span>

<span data-ttu-id="ca9d4-580">[Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)및 모든 관련 된 오버 로드는 컨트롤러 이름과 작업 이름을 지정 하 여 대상 끝점을 생성 하도록 디자인 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="ca9d4-581">를 사용 하는 경우 `Url.Action` 및에 대 한 현재 경로 값은 `controller` `action` 런타임에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="ca9d4-582">및의 값 `controller` 은 `action` [앰비언트 값](#ambient) 과 값의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="ca9d4-583">메서드는 `Url.Action` 항상 및의 현재 값을 사용 하 `action` `controller` 고 현재 작업으로 라우팅되는 URL 경로를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="ca9d4-584">라우팅은 앰비언트 값의 값을 사용 하 여 URL을 생성할 때 제공 되지 않은 정보를 채우도록 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="ca9d4-585">앰비언트 값과 같은 경로를 고려 합니다 `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="ca9d4-586">라우팅에는 추가 값 없이 URL을 생성 하는 데 충분 한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="ca9d4-587">모든 경로 매개 변수에는 값이 있으므로 라우팅에 충분 한 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="ca9d4-588">값 `{ d = Donovan }` 이 추가 된 경우:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="ca9d4-589">값은 `{ d = David }` 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="ca9d4-590">생성 된 URL 경로는 `Alice/Bob/Carol/Donovan` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="ca9d4-591">**경고**: URL 경로는 계층 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="ca9d4-592">앞의 예제에서 다음과 같이 값을 `{ c = Cheryl }` 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="ca9d4-593">두 값은 모두 `{ c = Carol, d = David }` 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="ca9d4-594">에 대 한 값이 더 이상 없으며 `d` URL 생성이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="ca9d4-595">`c`URL을 생성 하려면 및의 원하는 값을 `d` 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="ca9d4-596">기본 경로를 사용 하 여이 문제가 발생할 수 있습니다 `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="ca9d4-597">`Url.Action`항상 명시적으로 및 값을 지정 하기 때문에이 문제는 드물게 발생 `controller` `action` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="ca9d4-598">Url의 여러 오버 로드. Action은 경로 값 개체를 사용 하 여 및 이외의 경로 매개 변수에 대 한 값을 제공 [합니다.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) `controller` `action`</span><span class="sxs-lookup"><span data-stu-id="ca9d4-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ca9d4-599">경로 값 개체는와 함께 자주 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="ca9d4-600">`Url.Action("Buy", "Products", new { id = 17 })`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ca9d4-601">경로 값 개체:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-601">The route values object:</span></span>

* <span data-ttu-id="ca9d4-602">규칙에 따라 일반적으로 무명 형식의 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="ca9d4-603">`IDictionary<>`또는 [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="ca9d4-604">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ca9d4-605">위의 코드는을 생성 `/Products/Buy/17?color=red` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="ca9d4-606">다음 코드에서는 절대 URL을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="ca9d4-607">절대 URL을 만들려면 다음 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="ca9d4-608">을 허용 하는 오버 로드 `protocol` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="ca9d4-609">예를 들어 앞의 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-609">For example, the preceding code.</span></span>
* <span data-ttu-id="ca9d4-610">[Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)은 기본적으로 절대 uri를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="ca9d4-611">경로로 Url 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-611">Generate URLs by route</span></span>

<span data-ttu-id="ca9d4-612">위의 코드는 컨트롤러 및 작업 이름을 전달 하 여 URL을 생성 하는 방법을 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ca9d4-613">`IUrlHelper`또한 [RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 메서드 패밀리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="ca9d4-614">이러한 메서드는 [Url](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)과 유사 하지만 및의 현재 값을 `action` 경로 값에 복사 하지 않습니다 `controller` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ca9d4-615">의 가장 일반적인 사용법은 `Url.RouteUrl` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="ca9d4-616">URL을 생성 하는 경로 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="ca9d4-617">일반적으로 컨트롤러 또는 작업 이름을 지정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-618">다음 Razor 파일은에 대 한 HTML 링크를 생성 합니다 `Destination_Route` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="ca9d4-619">HTML로 Url 생성Razor</span><span class="sxs-lookup"><span data-stu-id="ca9d4-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="ca9d4-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>및 요소를 각각 생성 하는 [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) [html.beginform](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 및 html.actionlink 메서드를 제공 합니다 `<form>` `<a>` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ca9d4-621">이러한 메서드는 url을 생성 하는 데 [url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 메서드를 사용 하 여 비슷한 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ca9d4-622">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ca9d4-623">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ca9d4-624">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ca9d4-625">자세한 내용은 [양식의 태그 도우미](xref:mvc/views/working-with-forms) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="ca9d4-626">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="ca9d4-627">작업 결과의 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-627">URL generation in Action Results</span></span>

<span data-ttu-id="ca9d4-628">위의 예제는 컨트롤러에서를 사용 하는 방법을 보여 주었습니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="ca9d4-629">컨트롤러에서 가장 일반적으로 사용 되는 작업은 URL을 작업 결과의 일부로 생성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ca9d4-630"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> 및 <xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ca9d4-631">일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="ca9d4-632">및와 같은 작업 결과 팩터리 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> 는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 의 메서드와 유사한 패턴을 따릅니다 `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ca9d4-633">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="ca9d4-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ca9d4-634">[기존 라우팅은](#cr) [전용 기본 경로](#dcr)라고 하는 특별 한 종류의 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="ca9d4-635">다음 예제에서 라는 경로는 `blog` 전용 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-636">위의 경로 정의를 사용 하 여 `Url.Action("Index", "Home")` 경로를 사용 하 여 URL 경로를 생성 `/` `default` 하지만 이유는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="ca9d4-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="ca9d4-637">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ca9d4-638">[전용 기본 경로](#dcr) 는 URL 생성을 사용 하 여 경로가 너무 [non-greedy](xref:fundamentals/routing#greedy) 하는 것을 방지 하는 해당 경로 매개 변수가 없는 기본 값의 특수 한 동작을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="ca9d4-639">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ca9d4-640">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ca9d4-641">`blog`값이 일치 하지 않으므로를 사용한 URL 생성이 실패 `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ca9d4-642">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ca9d4-643">Areas</span><span class="sxs-lookup"><span data-stu-id="ca9d4-643">Areas</span></span>

<span data-ttu-id="ca9d4-644">[영역은](xref:mvc/controllers/areas) 별도의 그룹으로 관련 기능을 구성 하는 데 사용 되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="ca9d4-645">컨트롤러 작업에 대 한 라우팅 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="ca9d4-646">보기에 대 한 폴더 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-646">Folder structure for views.</span></span>

<span data-ttu-id="ca9d4-647">영역을 사용 하면 서로 다른 영역이 있는 한 앱에 동일한 이름의 여러 컨트롤러가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="ca9d4-648">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ca9d4-649">이 섹션에서는 라우팅이 영역과 상호 작용 하는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="ca9d4-650">영역을 뷰에서 사용 하는 방법에 대 한 자세한 내용은 [영역](xref:mvc/controllers/areas) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="ca9d4-651">다음 예에서는 기본 기본 경로와 명명 된의 경로를 사용 하도록 MVC를 구성 합니다 `area` `area` `Blog` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ca9d4-652">위의 코드에서를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 만들기 위해가 호출 됩니다 `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="ca9d4-653">두 번째 매개 변수인는 `"Blog"` 영역 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="ca9d4-654">URL 경로와 일치 하는 경우 경로 `/Manage/Users/AddUser` 는 `"blog_route"` 경로 값을 생성 합니다 `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ca9d4-655">`area`경로 값은에 대 한 기본값으로 생성 됩니다 `area` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="ca9d4-656">에서 만든 경로는 `MapAreaControllerRoute` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="ca9d4-657">`MapAreaControllerRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ca9d4-658">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="ca9d4-659">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca9d4-660">일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ca9d4-661">위의 예제를 사용 하면 경로 값이 `{ area = Blog, controller = Users, action = AddUser }` 다음 동작과 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ca9d4-662">[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성은 컨트롤러를 영역의 일부로 나타내는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="ca9d4-663">이 컨트롤러는 영역에 `Blog` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="ca9d4-664">특성이 없는 컨트롤러 `[Area]` 는 어떤 영역의 구성원도 아니므로 **not** `area` 경로 값이 라우팅에서 제공 되는 경우 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ca9d4-665">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="ca9d4-666">각 컨트롤러의 네임 스페이스는 완전성을 위해 여기에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="ca9d4-667">이전 컨트롤러에서 동일한 네임 스페이스를 사용 하는 경우 컴파일러 오류가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="ca9d4-668">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ca9d4-669">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ca9d4-670">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="ca9d4-671">매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ca9d4-672">영역 내에서 작업을 실행 하는 경우의 경로 값은 `area` URL 생성에 사용할 라우팅에 대 한 [앰비언트 값](#ambient) 으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="ca9d4-673">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="ca9d4-674">다음 코드에서는에 대 한 URL을 생성 합니다 `/Zebra/Users/AddUser` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="ca9d4-675">작업 정의</span><span class="sxs-lookup"><span data-stu-id="ca9d4-675">Action definition</span></span>

<span data-ttu-id="ca9d4-676">[비 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 특성을 사용 하는 경우를 제외 하 고 컨트롤러의 공용 메서드는 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ca9d4-677">예제 코드</span><span class="sxs-lookup"><span data-stu-id="ca9d4-677">Sample code</span></span>

 * <span data-ttu-id="ca9d4-678">[MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) 메서드는 [샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) 에 포함 되어 있으며 라우팅 정보를 표시 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="ca9d4-679">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca9d4-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca9d4-680">ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="ca9d4-681">경로는 시작 코드 또는 특성에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="ca9d4-682">경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="ca9d4-683">경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="ca9d4-684">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca9d4-685">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca9d4-686">자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ca9d4-687">이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="ca9d4-688">고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="ca9d4-689">라우팅 미들웨어 설정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="ca9d4-690">*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca9d4-691">`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="ca9d4-692">대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="ca9d4-693">`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 `/Products/Details/5`와 같은 URL 경로를 매칭하고 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ca9d4-694">MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="ca9d4-695">이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="ca9d4-696">자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="ca9d4-697">`default` 경로를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ca9d4-698">경로 템플릿의:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-698">The route template:</span></span>

* <span data-ttu-id="ca9d4-699">`{controller=Home}`는 `Home`를 기본 `controller`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="ca9d4-700">`{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="ca9d4-701">`{id?}`는 `id`를 선택 사항으로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="ca9d4-702">기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ca9d4-703">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="ca9d4-704">`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="ca9d4-705">`controller` 및 `action` 값으로 기본값이 사용되며, `id`는 URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ca9d4-706">MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ca9d4-707">이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="ca9d4-708">편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="ca9d4-709">다음을 대체할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca9d4-710">`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="ca9d4-711">MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="ca9d4-712">MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="ca9d4-713">`UseMvc` 내부의 코드는 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="ca9d4-714">`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="ca9d4-715">`UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="ca9d4-716">`UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="ca9d4-717">`UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="ca9d4-718">[특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ca9d4-719">규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-719">Conventional routing</span></span>

<span data-ttu-id="ca9d4-720">`default` 경로인:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ca9d4-721">위의 코드는 기존 라우팅의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="ca9d4-722">이 스타일은 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 기존 라우팅 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ca9d4-723">첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="ca9d4-724">두 번째는 작업 이름에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-724">The second maps to the action name.</span></span>
* <span data-ttu-id="ca9d4-725">세 번째 세그먼트는 선택 사항에 사용 됩니다 `id` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="ca9d4-726">`id`모델 엔터티에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="ca9d4-727">이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="ca9d4-728">매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="ca9d4-729">기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 애플리케이션을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="ca9d4-730">CRUD 스타일의 작업을 수행하는 애플리케이션의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ca9d4-731">`id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="ca9d4-732">일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="ca9d4-733">특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ca9d4-734">일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="ca9d4-735">여러 경로</span><span class="sxs-lookup"><span data-stu-id="ca9d4-735">Multiple routes</span></span>

<span data-ttu-id="ca9d4-736">`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="ca9d4-737">이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca9d4-738">여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로*입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="ca9d4-739">`controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ca9d4-740">경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="ca9d4-741">따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-742">*전용 기본 경로* 는 URL 경로의 나머지 부분을 캡처하는 등 **의 모든** 경로 매개 변수를 사용 하는 경우가 많습니다 `{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="ca9d4-743">이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ca9d4-744">이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="ca9d4-745">대체</span><span class="sxs-lookup"><span data-stu-id="ca9d4-745">Fallback</span></span>

<span data-ttu-id="ca9d4-746">요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="ca9d4-747">경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="ca9d4-748">이것을 *대체*라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="ca9d4-749">명확한 작업 구분</span><span class="sxs-lookup"><span data-stu-id="ca9d4-749">Disambiguating actions</span></span>

<span data-ttu-id="ca9d4-750">두 작업이 라우팅을 통해 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="ca9d4-751">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="ca9d4-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="ca9d4-752">이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="ca9d4-753">이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="ca9d4-754">이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="ca9d4-755">`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ca9d4-756">`IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="ca9d4-757">개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="ca9d4-758">규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="ca9d4-759">[IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="ca9d4-760">여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="ca9d4-761">경로 이름</span><span class="sxs-lookup"><span data-stu-id="ca9d4-761">Route names</span></span>

<span data-ttu-id="ca9d4-762">다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca9d4-763">경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="ca9d4-764">이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ca9d4-765">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ca9d4-766">경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="ca9d4-767">[라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="ca9d4-768">특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-768">Attribute routing</span></span>

<span data-ttu-id="ca9d4-769">특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ca9d4-770">다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="ca9d4-771">`HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="ca9d4-772">`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-773">이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="ca9d4-774">특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ca9d4-775">그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).</span><span class="sxs-lookup"><span data-stu-id="ca9d4-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="ca9d4-776">특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="ca9d4-777">이 예제는 이전 예제와 동일한 URL을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-777">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="ca9d4-778">위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="ca9d4-779">사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="ca9d4-780">경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="ca9d4-781">Http[Verb] 특성을 사용한 특성 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="ca9d4-782">특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="ca9d4-783">이러한 특성은 모두 경로 템플릿을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="ca9d4-784">이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-784">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="ca9d4-785">`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ca9d4-786">특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="ca9d4-787">경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="ca9d4-788">REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="ca9d4-789">보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="ca9d4-790">REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ca9d4-791">특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ca9d4-792">이 예제에서 `id`는 URL 경로에 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ca9d4-793">`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="ca9d4-794">경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="ca9d4-795">경로 이름</span><span class="sxs-lookup"><span data-stu-id="ca9d4-795">Route Name</span></span>

<span data-ttu-id="ca9d4-796">다음 코드는 `Products_List`의 *경로 이름*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ca9d4-797">경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ca9d4-798">경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="ca9d4-799">경로 이름은 애플리케이션 전체에서 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-800">`id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로*와는 대조적입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ca9d4-801">API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="ca9d4-802">경로 결합</span><span class="sxs-lookup"><span data-stu-id="ca9d4-802">Combining routes</span></span>

<span data-ttu-id="ca9d4-803">특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ca9d4-804">컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ca9d4-805">경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ca9d4-806">이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="ca9d4-807">두 작업은 모두 `HttpGetAttribute`로 표시되기 때문에 HTTP `GET`만 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="ca9d4-808">작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ca9d4-809">이 예제는 *기본 경로*와 비슷한 URL 경로 집합을 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-809">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="ca9d4-810">특성 경로 순서 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-810">Ordering attribute routes</span></span>

<span data-ttu-id="ca9d4-811">정의 된 순서로 실행 되는 기존 경로와 달리 특성 라우팅은 트리를 작성 하 고 모든 경로를 동시에 일치 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="ca9d4-812">이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ca9d4-813">예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="ca9d4-814">논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="ca9d4-815">규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ca9d4-816">특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="ca9d4-817">경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ca9d4-818">기본 순서는 `0`입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-818">The default order is `0`.</span></span> <span data-ttu-id="ca9d4-819">`Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="ca9d4-820">`Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="ca9d4-821">`Order`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="ca9d4-822">URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ca9d4-823">일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="ca9d4-824">URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 재정의로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="ca9d4-825"> Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ca9d4-826">페이지 항목의 경로 순서에 대 한 정보 Razor 는 [ Razor 페이지 경로 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ca9d4-827">경로 템플릿에서 토큰 바꾸기([컨트롤러] [작업] [지역])</span><span class="sxs-lookup"><span data-stu-id="ca9d4-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="ca9d4-828">편의를 위해 특성 경로는 토큰을 대괄호 (,)로 묶어 *토큰 바꾸기를* 지원 `[` `]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="ca9d4-829">`[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="ca9d4-830">다음 예의 작업은 주석에 설명된 대로 URL 경로를 매칭합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ca9d4-831">토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ca9d4-832">위의 예제는 다음 코드와 동일하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ca9d4-833">특성 경로를 상속과 결합할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ca9d4-834">특히 토큰 교체와 결합하면 더욱 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-834">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="ca9d4-835">토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="ca9d4-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="ca9d4-837">리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ca9d4-838">매개 변수 변환기를 사용하여 토큰 교체 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ca9d4-839">매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ca9d4-840">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ca9d4-841">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ca9d4-842">`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="ca9d4-843">애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ca9d4-844">대체되는 특성 경로 토큰 값을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="ca9d4-845">`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="ca9d4-846">여러 경로</span><span class="sxs-lookup"><span data-stu-id="ca9d4-846">Multiple Routes</span></span>

<span data-ttu-id="ca9d4-847">특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ca9d4-848">가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로*의 동작을 모방하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="ca9d4-849">컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="ca9d4-850">여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="ca9d4-851">작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 애플리케이션의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="ca9d4-852">기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ca9d4-853">특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ca9d4-854">특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="ca9d4-855">경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ca9d4-856">`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="ca9d4-857">프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="ca9d4-858">앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ca9d4-859">개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="ca9d4-860">각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="ca9d4-861">위의 예제의 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ca9d4-862">응용 프로그램 모델을 사용하여 특성 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca9d4-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="ca9d4-863">*응용 프로그램 모델*은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="ca9d4-864">*응용 프로그램 모델*은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="ca9d4-865">개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙*을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="ca9d4-866">이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ca9d4-867">혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅</span><span class="sxs-lookup"><span data-stu-id="ca9d4-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ca9d4-868">MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ca9d4-869">일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ca9d4-870">작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ca9d4-871">컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ca9d4-872">특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ca9d4-873">컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-874">두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="ca9d4-875">규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="ca9d4-876">특성 라우팅에서 각 템플릿은 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="ca9d4-877">복잡한 세그먼트</span><span class="sxs-lookup"><span data-stu-id="ca9d4-877">Complex segments</span></span>

<span data-ttu-id="ca9d4-878">복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="ca9d4-879">자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="ca9d4-880">자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/8197)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="ca9d4-881">URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-881">URL Generation</span></span>

<span data-ttu-id="ca9d4-882">MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ca9d4-883">URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="ca9d4-884">이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="ca9d4-885">URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ca9d4-886">`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ca9d4-887">컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ca9d4-888">이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-889">응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ca9d4-890">이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ca9d4-891">경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ca9d4-892">값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼).</span><span class="sxs-lookup"><span data-stu-id="ca9d4-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="ca9d4-893">필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ca9d4-894">경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ca9d4-895">위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="ca9d4-896">규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙*을 따르기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="ca9d4-897">특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="ca9d4-898">이 예제는 특성 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="ca9d4-899">MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="ca9d4-900">위의 예제에서는 `custom/url/to/destination`이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ca9d4-901">작업 이름으로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-901">Generating URLs by action name</span></span>

<span data-ttu-id="ca9d4-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ca9d4-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="ca9d4-903">`Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-904">`Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값*의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="ca9d4-905">`Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="ca9d4-906">라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="ca9d4-907">`{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="ca9d4-908">`{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="ca9d4-909">URL 경로는 계층적입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-909">URL paths are hierarchical.</span></span> <span data-ttu-id="ca9d4-910">위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="ca9d4-911">이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="ca9d4-912">원하는 `c` 및 `d` 값을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="ca9d4-913">기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="ca9d4-914">`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ca9d4-915">`Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ca9d4-916">관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체*일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="ca9d4-917">경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="ca9d4-918">절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="ca9d4-919">경로로 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-919">Generating URLs by route</span></span>

<span data-ttu-id="ca9d4-920">위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ca9d4-921">`IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="ca9d4-922">이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ca9d4-923">가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="ca9d4-924">HTML에서 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-924">Generating URLs in HTML</span></span>

<span data-ttu-id="ca9d4-925">`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ca9d4-926">이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ca9d4-927">`HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ca9d4-928">TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ca9d4-929">둘 다 구현에 `IUrlHelper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ca9d4-930">자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="ca9d4-931">보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="ca9d4-932">작업 결과에 URL 생성</span><span class="sxs-lookup"><span data-stu-id="ca9d4-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="ca9d4-933">위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ca9d4-934">`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ca9d4-935">한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-935">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="ca9d4-936">작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ca9d4-937">전용 규칙 기반 경로의 특별한 사례</span><span class="sxs-lookup"><span data-stu-id="ca9d4-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ca9d4-938">규칙 기반 라우팅은 *전용 규칙 기반 경로*라고 하는 특별한 경로 정의를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="ca9d4-939">아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ca9d4-940">이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요?</span><span class="sxs-lookup"><span data-stu-id="ca9d4-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="ca9d4-941">`{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ca9d4-942">전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="ca9d4-943">이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ca9d4-944">라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ca9d4-945">`{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ca9d4-946">그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ca9d4-947">Areas</span><span class="sxs-lookup"><span data-stu-id="ca9d4-947">Areas</span></span>

<span data-ttu-id="ca9d4-948">[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="ca9d4-949">영역을 사용하면 컨트롤러의 *영역*이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="ca9d4-950">영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ca9d4-951">이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="ca9d4-952">다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로*를 사용하도록 MVC를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ca9d4-953">`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ca9d4-954">`area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="ca9d4-955">`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ca9d4-956">기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="ca9d4-957">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ca9d4-958">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ca9d4-959">위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ca9d4-960">`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="ca9d4-961">`[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ca9d4-962">다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="ca9d4-963">각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="ca9d4-964">클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ca9d4-965">처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ca9d4-966">세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-967">매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ca9d4-968">영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값*으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="ca9d4-969">즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="ca9d4-970">IActionConstraint 이해</span><span class="sxs-lookup"><span data-stu-id="ca9d4-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="ca9d4-971">이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="ca9d4-972">일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="ca9d4-973">이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="ca9d4-974">`[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="ca9d4-975">기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두**와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="ca9d4-976">`IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="ca9d4-977">`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET*에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="ca9d4-978">`Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="ca9d4-979">따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="ca9d4-980">하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="ca9d4-981">따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="ca9d4-982">개념적으로 `IActionConstraint`는 *오버로딩*의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="ca9d4-983">특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="ca9d4-984">IActionConstraint 구현</span><span class="sxs-lookup"><span data-stu-id="ca9d4-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="ca9d4-985">`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="ca9d4-986">MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="ca9d4-987">애플리케이션 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="ca9d4-988">다음 예에서는 제약 조건이 경로 데이터의 *국가 코드* 를 기반으로 하는 작업을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="ca9d4-989">[GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="ca9d4-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="ca9d4-990">개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="ca9d4-991">이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="ca9d4-992">비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="ca9d4-993">이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="ca9d4-994">`Order` 속성은 제약 조건이 소속되는 *단계*를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="ca9d4-995">작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="ca9d4-996">예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="ca9d4-997">원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="ca9d4-998">`Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="ca9d4-999">숫자가 낮을수록 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca9d4-999">Lower numbers run first.</span></span>

::: moniker-end
