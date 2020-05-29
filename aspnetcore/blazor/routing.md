---
<span data-ttu-id="fb985-101">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-102">'Blazor'</span></span>
- <span data-ttu-id="fb985-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-103">'Identity'</span></span>
- <span data-ttu-id="fb985-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-105">'Razor'</span></span>
- <span data-ttu-id="fb985-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="fb985-107">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="fb985-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="fb985-108">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="fb985-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fb985-109">요청을 라우팅하는 방법과 Blazor 앱에서 `NavLink` 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="fb985-110">ASP.NET Core 엔드포인트 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="fb985-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="fb985-111"> 서버는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="fb985-112">`Startup.Configure`의 `MapBlazorHub`를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="fb985-113">가장 일반적인 구성은 Blazor 서버 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="fb985-114">규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 *_Host.cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="fb985-115">호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="fb985-116">일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="fb985-117">이렇게 하면 앱이 Blazor 서버 앱을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="fb985-118">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="fb985-118">Route templates</span></span>

<span data-ttu-id="fb985-119">`Router` 구성 요소는 지정된 경로를 사용하여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="fb985-120">`Router` 구성 요소는 *App.razor* 파일에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-120">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="fb985-121">`@page` 지시문을 포함하는 *.razor* 파일을 컴파일하면, 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="fb985-122">런타임에 `RouteView` 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="fb985-123">원하는 매개 변수와 함께 `Router`에서 `RouteData`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="fb985-124">지정된 매개 변수를 사용하여 지정된 구성 요소를 해당 레이아웃(또는 선택적 기본 레이아웃)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="fb985-125">필요한 경우, 레이아웃을 지정하지 않는 구성 요소에 사용할 `DefaultLayout` 매개 변수를 레이아웃 클래스로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="fb985-126">기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="fb985-127">*MainLayout.razor*는 템플릿 프로젝트의 *Shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="fb985-128">레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="fb985-129">한 구성 요소에 여러 개의 경로 템플릿을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="fb985-130">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="fb985-131">URL이 올바르게 확인되려면 앱의 *wwwroot/index.html* 파일(Blazor WebAssembly) 또는 *Pages/_Host.cshtml* 파일(Blazor 서버)에 `<base>` 태그가 있어야 하고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다(`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="fb985-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="fb985-132">자세한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="fb985-133">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="fb985-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="fb985-134">`Router` 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="fb985-135">*App.razor* 파일에서 `Router` 구성 요소의 `NotFound` 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="fb985-136">`<NotFound>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="fb985-137">`NotFound` 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="fb985-138">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="fb985-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="fb985-139">`AdditionalAssemblies` 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 `Router` 구성 요소에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="fb985-140">지정한 어셈블리는 `AppAssembly`에서 지정한 어셈블리에 추가로 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="fb985-141">다음 예제에서 `Component1`은 참조된 클래스 라이브러리에서 정의된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="fb985-142">다음 `AdditionalAssemblies` 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="fb985-143">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="fb985-143">Route parameters</span></span>

<span data-ttu-id="fb985-144">라우터는 경로 매개 변수를 사용하여 해당 구성 요소 매개 변수를 동일한 이름(대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="fb985-145">선택적 매개 변수는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="fb985-146">위의 예제에서는 두 개의 `@page` 지시문이 적용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="fb985-147">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="fb985-148">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용하고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="fb985-149">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="fb985-149">Route constraints</span></span>

<span data-ttu-id="fb985-150">경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="fb985-151">다음 예제에서 `Users` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="fb985-152">요청 URL에 `Id` 경로 세그먼트가 있는 경우</span><span class="sxs-lookup"><span data-stu-id="fb985-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="fb985-153">`Id` 세그먼트가 정수(`int`)인 경우</span><span class="sxs-lookup"><span data-stu-id="fb985-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="fb985-154">다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="fb985-155">고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="fb985-156">제약 조건</span><span class="sxs-lookup"><span data-stu-id="fb985-156">Constraint</span></span> | <span data-ttu-id="fb985-157">예제</span><span class="sxs-lookup"><span data-stu-id="fb985-157">Example</span></span>           | <span data-ttu-id="fb985-158">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="fb985-158">Example Matches</span></span>                                                                  | <span data-ttu-id="fb985-159">고정</span><span class="sxs-lookup"><span data-stu-id="fb985-159">Invariant</span></span><br><span data-ttu-id="fb985-160">culture</span><span class="sxs-lookup"><span data-stu-id="fb985-160">culture</span></span><br><span data-ttu-id="fb985-161">일치</span><span class="sxs-lookup"><span data-stu-id="fb985-161">matching</span></span> |
| ---
<span data-ttu-id="fb985-162">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-163">'Blazor'</span></span>
- <span data-ttu-id="fb985-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-164">'Identity'</span></span>
- <span data-ttu-id="fb985-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-166">'Razor'</span></span>
- <span data-ttu-id="fb985-167">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-168">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-169">'Blazor'</span></span>
- <span data-ttu-id="fb985-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-170">'Identity'</span></span>
- <span data-ttu-id="fb985-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-172">'Razor'</span></span>
- <span data-ttu-id="fb985-173">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-174">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-175">'Blazor'</span></span>
- <span data-ttu-id="fb985-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-176">'Identity'</span></span>
- <span data-ttu-id="fb985-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-178">'Razor'</span></span>
- <span data-ttu-id="fb985-179">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-179">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-180">----- | --- title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-181">'Blazor'</span></span>
- <span data-ttu-id="fb985-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-182">'Identity'</span></span>
- <span data-ttu-id="fb985-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-184">'Razor'</span></span>
- <span data-ttu-id="fb985-185">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-186">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-187">'Blazor'</span></span>
- <span data-ttu-id="fb985-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-188">'Identity'</span></span>
- <span data-ttu-id="fb985-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-190">'Razor'</span></span>
- <span data-ttu-id="fb985-191">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-192">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-193">'Blazor'</span></span>
- <span data-ttu-id="fb985-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-194">'Identity'</span></span>
- <span data-ttu-id="fb985-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-196">'Razor'</span></span>
- <span data-ttu-id="fb985-197">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-198">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-199">'Blazor'</span></span>
- <span data-ttu-id="fb985-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-200">'Identity'</span></span>
- <span data-ttu-id="fb985-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-202">'Razor'</span></span>
- <span data-ttu-id="fb985-203">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-204">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-205">'Blazor'</span></span>
- <span data-ttu-id="fb985-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-206">'Identity'</span></span>
- <span data-ttu-id="fb985-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-208">'Razor'</span></span>
- <span data-ttu-id="fb985-209">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-210">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-211">'Blazor'</span></span>
- <span data-ttu-id="fb985-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-212">'Identity'</span></span>
- <span data-ttu-id="fb985-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-214">'Razor'</span></span>
- <span data-ttu-id="fb985-215">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-215">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-216">--------- | --- title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-217">'Blazor'</span></span>
- <span data-ttu-id="fb985-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-218">'Identity'</span></span>
- <span data-ttu-id="fb985-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-220">'Razor'</span></span>
- <span data-ttu-id="fb985-221">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-222">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-223">'Blazor'</span></span>
- <span data-ttu-id="fb985-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-224">'Identity'</span></span>
- <span data-ttu-id="fb985-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-226">'Razor'</span></span>
- <span data-ttu-id="fb985-227">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-228">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-229">'Blazor'</span></span>
- <span data-ttu-id="fb985-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-230">'Identity'</span></span>
- <span data-ttu-id="fb985-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-232">'Razor'</span></span>
- <span data-ttu-id="fb985-233">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-234">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-235">'Blazor'</span></span>
- <span data-ttu-id="fb985-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-236">'Identity'</span></span>
- <span data-ttu-id="fb985-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-238">'Razor'</span></span>
- <span data-ttu-id="fb985-239">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-240">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-241">'Blazor'</span></span>
- <span data-ttu-id="fb985-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-242">'Identity'</span></span>
- <span data-ttu-id="fb985-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-244">'Razor'</span></span>
- <span data-ttu-id="fb985-245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-246">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-247">'Blazor'</span></span>
- <span data-ttu-id="fb985-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-248">'Identity'</span></span>
- <span data-ttu-id="fb985-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-250">'Razor'</span></span>
- <span data-ttu-id="fb985-251">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-252">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-253">'Blazor'</span></span>
- <span data-ttu-id="fb985-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-254">'Identity'</span></span>
- <span data-ttu-id="fb985-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-256">'Razor'</span></span>
- <span data-ttu-id="fb985-257">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-258">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-259">'Blazor'</span></span>
- <span data-ttu-id="fb985-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-260">'Identity'</span></span>
- <span data-ttu-id="fb985-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-262">'Razor'</span></span>
- <span data-ttu-id="fb985-263">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-264">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-265">'Blazor'</span></span>
- <span data-ttu-id="fb985-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-266">'Identity'</span></span>
- <span data-ttu-id="fb985-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-268">'Razor'</span></span>
- <span data-ttu-id="fb985-269">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-270">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-271">'Blazor'</span></span>
- <span data-ttu-id="fb985-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-272">'Identity'</span></span>
- <span data-ttu-id="fb985-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-274">'Razor'</span></span>
- <span data-ttu-id="fb985-275">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-276">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-277">'Blazor'</span></span>
- <span data-ttu-id="fb985-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-278">'Identity'</span></span>
- <span data-ttu-id="fb985-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-280">'Razor'</span></span>
- <span data-ttu-id="fb985-281">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-282">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-283">'Blazor'</span></span>
- <span data-ttu-id="fb985-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-284">'Identity'</span></span>
- <span data-ttu-id="fb985-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-286">'Razor'</span></span>
- <span data-ttu-id="fb985-287">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-288">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-289">'Blazor'</span></span>
- <span data-ttu-id="fb985-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-290">'Identity'</span></span>
- <span data-ttu-id="fb985-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-292">'Razor'</span></span>
- <span data-ttu-id="fb985-293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-294">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-295">'Blazor'</span></span>
- <span data-ttu-id="fb985-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-296">'Identity'</span></span>
- <span data-ttu-id="fb985-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-298">'Razor'</span></span>
- <span data-ttu-id="fb985-299">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-300">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-301">'Blazor'</span></span>
- <span data-ttu-id="fb985-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-302">'Identity'</span></span>
- <span data-ttu-id="fb985-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-304">'Razor'</span></span>
- <span data-ttu-id="fb985-305">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-306">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-307">'Blazor'</span></span>
- <span data-ttu-id="fb985-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-308">'Identity'</span></span>
- <span data-ttu-id="fb985-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-310">'Razor'</span></span>
- <span data-ttu-id="fb985-311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-312">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-313">'Blazor'</span></span>
- <span data-ttu-id="fb985-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-314">'Identity'</span></span>
- <span data-ttu-id="fb985-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-316">'Razor'</span></span>
- <span data-ttu-id="fb985-317">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-318">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-319">'Blazor'</span></span>
- <span data-ttu-id="fb985-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-320">'Identity'</span></span>
- <span data-ttu-id="fb985-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-322">'Razor'</span></span>
- <span data-ttu-id="fb985-323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-324">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-325">'Blazor'</span></span>
- <span data-ttu-id="fb985-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-326">'Identity'</span></span>
- <span data-ttu-id="fb985-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-328">'Razor'</span></span>
- <span data-ttu-id="fb985-329">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-330">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-331">'Blazor'</span></span>
- <span data-ttu-id="fb985-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-332">'Identity'</span></span>
- <span data-ttu-id="fb985-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-334">'Razor'</span></span>
- <span data-ttu-id="fb985-335">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-336">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-337">'Blazor'</span></span>
- <span data-ttu-id="fb985-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-338">'Identity'</span></span>
- <span data-ttu-id="fb985-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-340">'Razor'</span></span>
- <span data-ttu-id="fb985-341">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-342">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-343">'Blazor'</span></span>
- <span data-ttu-id="fb985-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-344">'Identity'</span></span>
- <span data-ttu-id="fb985-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-346">'Razor'</span></span>
- <span data-ttu-id="fb985-347">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-348">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-349">'Blazor'</span></span>
- <span data-ttu-id="fb985-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-350">'Identity'</span></span>
- <span data-ttu-id="fb985-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-352">'Razor'</span></span>
- <span data-ttu-id="fb985-353">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-354">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-355">'Blazor'</span></span>
- <span data-ttu-id="fb985-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-356">'Identity'</span></span>
- <span data-ttu-id="fb985-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-358">'Razor'</span></span>
- <span data-ttu-id="fb985-359">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-360">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-361">'Blazor'</span></span>
- <span data-ttu-id="fb985-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-362">'Identity'</span></span>
- <span data-ttu-id="fb985-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-364">'Razor'</span></span>
- <span data-ttu-id="fb985-365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-366">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-367">'Blazor'</span></span>
- <span data-ttu-id="fb985-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-368">'Identity'</span></span>
- <span data-ttu-id="fb985-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-370">'Razor'</span></span>
- <span data-ttu-id="fb985-371">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-372">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-373">'Blazor'</span></span>
- <span data-ttu-id="fb985-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-374">'Identity'</span></span>
- <span data-ttu-id="fb985-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-376">'Razor'</span></span>
- <span data-ttu-id="fb985-377">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-378">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-379">'Blazor'</span></span>
- <span data-ttu-id="fb985-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-380">'Identity'</span></span>
- <span data-ttu-id="fb985-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-382">'Razor'</span></span>
- <span data-ttu-id="fb985-383">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-384">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-385">'Blazor'</span></span>
- <span data-ttu-id="fb985-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-386">'Identity'</span></span>
- <span data-ttu-id="fb985-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-388">'Razor'</span></span>
- <span data-ttu-id="fb985-389">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-390">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-391">'Blazor'</span></span>
- <span data-ttu-id="fb985-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-392">'Identity'</span></span>
- <span data-ttu-id="fb985-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-394">'Razor'</span></span>
- <span data-ttu-id="fb985-395">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-396">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-397">'Blazor'</span></span>
- <span data-ttu-id="fb985-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-398">'Identity'</span></span>
- <span data-ttu-id="fb985-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-400">'Razor'</span></span>
- <span data-ttu-id="fb985-401">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-402">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-403">'Blazor'</span></span>
- <span data-ttu-id="fb985-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-404">'Identity'</span></span>
- <span data-ttu-id="fb985-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-406">'Razor'</span></span>
- <span data-ttu-id="fb985-407">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-408">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-409">'Blazor'</span></span>
- <span data-ttu-id="fb985-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-410">'Identity'</span></span>
- <span data-ttu-id="fb985-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-412">'Razor'</span></span>
- <span data-ttu-id="fb985-413">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-414">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-415">'Blazor'</span></span>
- <span data-ttu-id="fb985-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-416">'Identity'</span></span>
- <span data-ttu-id="fb985-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-418">'Razor'</span></span>
- <span data-ttu-id="fb985-419">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-420">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-421">'Blazor'</span></span>
- <span data-ttu-id="fb985-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-422">'Identity'</span></span>
- <span data-ttu-id="fb985-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-424">'Razor'</span></span>
- <span data-ttu-id="fb985-425">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-426">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-427">'Blazor'</span></span>
- <span data-ttu-id="fb985-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-428">'Identity'</span></span>
- <span data-ttu-id="fb985-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-430">'Razor'</span></span>
- <span data-ttu-id="fb985-431">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-432">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-433">'Blazor'</span></span>
- <span data-ttu-id="fb985-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-434">'Identity'</span></span>
- <span data-ttu-id="fb985-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-436">'Razor'</span></span>
- <span data-ttu-id="fb985-437">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-438">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-439">'Blazor'</span></span>
- <span data-ttu-id="fb985-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-440">'Identity'</span></span>
- <span data-ttu-id="fb985-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-442">'Razor'</span></span>
- <span data-ttu-id="fb985-443">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-443">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-445">'Blazor'</span></span>
- <span data-ttu-id="fb985-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-446">'Identity'</span></span>
- <span data-ttu-id="fb985-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-448">'Razor'</span></span>
- <span data-ttu-id="fb985-449">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-450">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-451">'Blazor'</span></span>
- <span data-ttu-id="fb985-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-452">'Identity'</span></span>
- <span data-ttu-id="fb985-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-454">'Razor'</span></span>
- <span data-ttu-id="fb985-455">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-456">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-457">'Blazor'</span></span>
- <span data-ttu-id="fb985-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-458">'Identity'</span></span>
- <span data-ttu-id="fb985-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-460">'Razor'</span></span>
- <span data-ttu-id="fb985-461">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-462">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-463">'Blazor'</span></span>
- <span data-ttu-id="fb985-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-464">'Identity'</span></span>
- <span data-ttu-id="fb985-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-466">'Razor'</span></span>
- <span data-ttu-id="fb985-467">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-468">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-469">'Blazor'</span></span>
- <span data-ttu-id="fb985-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-470">'Identity'</span></span>
- <span data-ttu-id="fb985-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-472">'Razor'</span></span>
- <span data-ttu-id="fb985-473">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-474">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-475">'Blazor'</span></span>
- <span data-ttu-id="fb985-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-476">'Identity'</span></span>
- <span data-ttu-id="fb985-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-478">'Razor'</span></span>
- <span data-ttu-id="fb985-479">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-480">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-481">'Blazor'</span></span>
- <span data-ttu-id="fb985-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-482">'Identity'</span></span>
- <span data-ttu-id="fb985-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-484">'Razor'</span></span>
- <span data-ttu-id="fb985-485">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-486">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-487">'Blazor'</span></span>
- <span data-ttu-id="fb985-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-488">'Identity'</span></span>
- <span data-ttu-id="fb985-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-490">'Razor'</span></span>
- <span data-ttu-id="fb985-491">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-492">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-493">'Blazor'</span></span>
- <span data-ttu-id="fb985-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-494">'Identity'</span></span>
- <span data-ttu-id="fb985-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-496">'Razor'</span></span>
- <span data-ttu-id="fb985-497">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-498">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-499">'Blazor'</span></span>
- <span data-ttu-id="fb985-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-500">'Identity'</span></span>
- <span data-ttu-id="fb985-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-502">'Razor'</span></span>
- <span data-ttu-id="fb985-503">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-504">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-505">'Blazor'</span></span>
- <span data-ttu-id="fb985-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-506">'Identity'</span></span>
- <span data-ttu-id="fb985-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-508">'Razor'</span></span>
- <span data-ttu-id="fb985-509">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-510">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-511">'Blazor'</span></span>
- <span data-ttu-id="fb985-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-512">'Identity'</span></span>
- <span data-ttu-id="fb985-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-514">'Razor'</span></span>
- <span data-ttu-id="fb985-515">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-516">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-517">'Blazor'</span></span>
- <span data-ttu-id="fb985-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-518">'Identity'</span></span>
- <span data-ttu-id="fb985-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-520">'Razor'</span></span>
- <span data-ttu-id="fb985-521">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-521">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | 아니요                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | 예                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | 예                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | 예                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | 예                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 아니요                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | 예                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | 예                              |</span><span class="sxs-lookup"><span data-stu-id="fb985-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="fb985-523">CLR 형식(예: `int` 또는 `DateTime`)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="fb985-524">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="fb985-525">점이 포함된 URL을 사용한 라우팅</span><span class="sxs-lookup"><span data-stu-id="fb985-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="fb985-526">Blazor 서버 앱에서 *_Host.cshtml*의 기본 경로는 `/`(`@page "/"`)입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="fb985-527">점(`.`)이 포함된 요청 URL은 URL이 파일을 요청하는 것 같으므로 기본 경로와 일치되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="fb985-528">Blazor 앱은 존재하지 않는 정적 파일에 대해 *404 - 찾을 수 없음* 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="fb985-529">점이 포함된 경로를 사용하려면 다음 경로 템플릿을 통해 *_Host.cshtml*을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="fb985-530">`"/{**path}"` 템플릿에는 다음이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="fb985-531">슬래시(`/`)를 인코딩하지 않고 여러 폴더 경계에 걸쳐 있는 경로를 캡처하기 위한 이중 별표 *catch-all* 구문(`**`)</span><span class="sxs-lookup"><span data-stu-id="fb985-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="fb985-532">`path` 경로 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="fb985-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="fb985-533">*Catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="fb985-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="fb985-534">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="fb985-535">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="fb985-535">NavLink component</span></span>

<span data-ttu-id="fb985-536">탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 `NavLink` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="fb985-537">`NavLink` 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="fb985-538">`active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="fb985-539">다음 `NavMenu` 구성 요소는 `NavLink` 구성 요소를 사용하는 방법을 보여 주는 [부트스트랩](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="fb985-540">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 `NavLinkMatch` 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="fb985-541">`NavLinkMatch.All` - 현재 URL 전체와 일치하는 경우 `NavLink`가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="fb985-542">`NavLinkMatch.Prefix`(‘기본값’) - 현재 URL의 접두사와 일치하는 경우 `NavLink`가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="fb985-543">위의 예제에서 홈 `NavLink` `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="fb985-544">두 번째 `NavLink`는 사용자가 `MyComponent` 접두사를 포함하는 URL(예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)을 방문할 때 `active` 클래스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="fb985-545">추가 `NavLink` 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="fb985-546">다음 예제에서 `NavLink` 구성 요소는 `target` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="fb985-547">다음 HTML 태그가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="fb985-548">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="fb985-548">URI and navigation state helpers</span></span>

<span data-ttu-id="fb985-549"><xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI 및 탐색 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="fb985-550">`NavigationManager`는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="fb985-551">멤버</span><span class="sxs-lookup"><span data-stu-id="fb985-551">Member</span></span> | <span data-ttu-id="fb985-552">설명</span><span class="sxs-lookup"><span data-stu-id="fb985-552">Description</span></span> |
| ---
<span data-ttu-id="fb985-553">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-554">'Blazor'</span></span>
- <span data-ttu-id="fb985-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-555">'Identity'</span></span>
- <span data-ttu-id="fb985-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-557">'Razor'</span></span>
- <span data-ttu-id="fb985-558">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-558">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-559">--- | --- title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-560">'Blazor'</span></span>
- <span data-ttu-id="fb985-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-561">'Identity'</span></span>
- <span data-ttu-id="fb985-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-563">'Razor'</span></span>
- <span data-ttu-id="fb985-564">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-565">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-566">'Blazor'</span></span>
- <span data-ttu-id="fb985-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-567">'Identity'</span></span>
- <span data-ttu-id="fb985-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-569">'Razor'</span></span>
- <span data-ttu-id="fb985-570">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="fb985-571">title: 'ASP.NET Core Blazor 라우팅' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="fb985-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fb985-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fb985-572">'Blazor'</span></span>
- <span data-ttu-id="fb985-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fb985-573">'Identity'</span></span>
- <span data-ttu-id="fb985-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fb985-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="fb985-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fb985-575">'Razor'</span></span>
- <span data-ttu-id="fb985-576">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="fb985-576">'SignalR' uid:</span></span> 

<span data-ttu-id="fb985-577">------ | | Uri | 현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="fb985-578">| | BaseUri | 절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="fb985-579">일반적으로 `BaseUri`는 *wwwroot/index.html*(Blazor WebAssembly) 또는 *Pages/_Host.cshtml*(Blazor 서버)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="fb985-580">| | NavigateTo | 지정된 URI로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="fb985-581">`forceLoad`가 `true`인 경우</span><span class="sxs-lookup"><span data-stu-id="fb985-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="fb985-582">클라이언트 쪽 라우팅이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="fb985-583">클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="fb985-584">| | LocationChanged | 탐색 위치가 변경된 경우에 발생하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="fb985-585">| | ToAbsoluteUri | 상대 URI를 절대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="fb985-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | 기본 URI(예: 이전에 `GetBaseUri`에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="fb985-587">단추를 선택하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="fb985-588">다음 구성 요소는 위치 변경 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-588">The following component handles a location changed event.</span></span> <span data-ttu-id="fb985-589">`HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="fb985-590">메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="fb985-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 이벤트에 대해 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="fb985-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 새 위치의 URL</span><span class="sxs-lookup"><span data-stu-id="fb985-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="fb985-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; `true`인 경우 Blazor는 브라우저에서 탐색을 가로챕니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="fb985-594">`false`인 경우 [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A)에서 탐색을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fb985-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="fb985-595">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fb985-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
