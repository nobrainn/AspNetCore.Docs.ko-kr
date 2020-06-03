---
<span data-ttu-id="a62c1-101">title: 'ASP.NET Core Razor 구성 요소 만들기 및 사용' author: description: '데이터에 바인딩하고, 이벤트를 처리하고, 구성 요소 수명 주기를 관리하는 방법을 비롯하여 Razor 구성 요소를 만들고 사용하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="a62c1-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="a62c1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a62c1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a62c1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a62c1-103">'Blazor'</span></span>
- <span data-ttu-id="a62c1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a62c1-104">'Identity'</span></span>
- <span data-ttu-id="a62c1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a62c1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a62c1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a62c1-106">'Razor'</span></span>
- <span data-ttu-id="a62c1-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="a62c1-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="a62c1-108">ASP.NET Core Razor 구성 요소 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="a62c1-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="a62c1-109">작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) 및 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="a62c1-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="a62c1-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a62c1-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="a62c1-111"> 앱은 *구성 요소*를 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-111"> apps are built using *components*.</span></span> <span data-ttu-id="a62c1-112">구성 요소는 페이지, 대화 상자 또는 양식과 같은 UI(사용자 인터페이스)의 자체 포함 청크입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="a62c1-113">구성 요소는 데이터를 주입하거나 UI 이벤트에 응답하는 데 필요한 HTML 태그와 처리 논리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="a62c1-114">구성 요소는 유연하고 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="a62c1-115">프로젝트 간에 중첩, 재사용 및 공유될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="a62c1-116">구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="a62c1-116">Component classes</span></span>

<span data-ttu-id="a62c1-117">구성 요소는 C# 및 HTML 태그 조합을 사용하여 [Razor](xref:mvc/views/razor) 구성 요소 파일( *.razor*)에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="a62c1-118">Blazor의 구성 요소는 공식적으로 ‘Razor 구성 요소’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="a62c1-119">구성 요소의 이름은 대문자로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="a62c1-120">예를 들어, *MyCoolComponent.razor*는 유효하고 *myCoolComponent*는 유효하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="a62c1-121">구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="a62c1-122">동적 렌더링 논리(예: 루프, 조건, 식)는 *Razor* 라고 하는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="a62c1-123">앱이 컴파일되면 HTML 태그 및 C# 렌더링 논리는 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="a62c1-124">생성된 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="a62c1-125">구성 요소 클래스의 멤버는 [`@code`][1] 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="a62c1-126">[`@code`][1] 블록에서 구성 요소 상태(속성, 필드)는 이벤트 처리 또는 다른 구성 요소 논리 정의를 위한 메서드로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="a62c1-127">두 개 이상의 [`@code`][1] 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="a62c1-128">구성 요소 멤버는 `@`으로 시작되는 C# 식을 사용하는 구성 요소 렌더링 논리의 일부로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="a62c1-129">예를 들어, C# 필드는 필드 이름에 앞에 `@`을 붙여 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="a62c1-130">다음 예제는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="a62c1-131">`headingFontStyle`을 평가하고 `font-style`에 대한 CSS 속성 값으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="a62c1-132">`headingText`를 평가하고 `<h1>` 요소의 내용으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="a62c1-133">구성 요소가 처음 렌더링되면 구성 요소는 이벤트에 대한 응답으로 렌더링 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="a62c1-134">그런 후 Blazor는 새로운 렌더링 트리를 이전 렌터링 트리와 비교하여 수정 사항을 브라우저 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-134">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="a62c1-135">구성 요소는 일반 C# 클래스이며 프로젝트 내의 어느 위치에나 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="a62c1-136">웹 페이지를 생성하는 구성 요소는 일반적으로 *Pages* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="a62c1-137">페이지가 아닌 구성 요소는 *Shared* 폴더 또는 프로젝트에 추가된 사용자 지정 폴더에 자주 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="a62c1-138">일반적으로 구성 요소의 네임스페이스는 앱의 루트 네임스페이스와 앱 내의 구성 요소 위치(폴더)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="a62c1-139">앱의 루트 네임스페이스가 `BlazorApp`이고 `Counter` 구성 요소가 *Pages* 폴더에 있는 경우 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="a62c1-140">`Counter` 구성 요소의 네임스페이스는 `BlazorApp.Pages`입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="a62c1-141">구성 요소의 정규화된 형식 이름은 `BlazorApp.Pages.Counter`입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="a62c1-142">구성 요소를 포함하는 사용자 지정 폴더의 경우 부모 구성 요소 또는 앱의 *_Imports.razor* 파일에 `using` 문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="a62c1-143">다음 예에서는 ‘구성 요소’ 폴더의 구성 요소를 사용할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="a62c1-144">또는 구성 요소를 직접 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="a62c1-145">자세한 내용은 [구성 요소 가져오기](#import-components) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="a62c1-146"> 구문</span><span class="sxs-lookup"><span data-stu-id="a62c1-146"> syntax</span></span>

Blazor<span data-ttu-id="a62c1-147"> 앱의 Razor 구성 요소는 Razor 구문을 광범위하게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="a62c1-148">Razor 태그 언어에 익숙하지 않은 경우 계속하기 전에 <xref:mvc/views/razor>를 읽는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="a62c1-149">Razor 구문에서 콘텐츠에 액세스하는 경우 다음 섹션에 특히 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="a62c1-150">[지시문](xref:mvc/views/razor#directives): 일반적으로 구성 요소 태그가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-150">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="a62c1-151">[지시문 특성](xref:mvc/views/razor#directive-attributes): 일반적으로 구성 요소가 구문 분석되거나 작동하는 방식을 변경하는 `@` 접두사가 있는 예약 키워드입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-151">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="a62c1-152">정적 자산</span><span class="sxs-lookup"><span data-stu-id="a62c1-152">Static assets</span></span>

Blazor<span data-ttu-id="a62c1-153">는 프로젝트의 [웹 루트(wwwroot) 폴더](xref:fundamentals/index#web-root) 아래에 정적 자산을 배치하는 ASP.NET Core 앱의 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="a62c1-154">기본 상대 경로(`/`)를 사용하여 정적 자산의 웹 루트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="a62c1-155">다음 예제에서 *logo.png*는 실제로 *{PROJECT ROOT}/wwwroot/images* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="a62c1-156"> 구성 요소는 물결표-슬래시 표기법(`~/`)을 지원하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="a62c1-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="a62c1-157">앱의 기본 경로를 설정하는 방법에 대한 내용은 <xref:host-and-deploy/blazor/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="a62c1-158">태그 도우미는 구성 요소에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="a62c1-159">[태그 도우미](xref:mvc/views/tag-helpers/intro)는 Razor 구성 요소( *.razor* 파일)에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="a62c1-160">Blazor에서 태그 도우미와 유사한 기능을 제공하려면 대신, 태그 도우미와 동일한 기능을 포함하는 구성 요소를 만들고 해당 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="a62c1-161">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="a62c1-161">Use components</span></span>

<span data-ttu-id="a62c1-162">구성 요소는 HTML 요소 구문을 사용하여 선언함으로써 다른 구성 요소를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="a62c1-163">구성 요소 사용을 위한 태그는 태그 이름이 구성 요소 유형인 HTML 태그처럼 보입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="a62c1-164">*Index.razor*의 다음 태그는 `HeadingComponent` 인스턴스를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="a62c1-165">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="a62c1-166">구성 요소에 구성 요소 이름과 일치하지 않는 HTML 요소(첫 글자가 대문자임)가 포함되면 요소에 예기치 않은 이름이 있음을 나타내는 경고가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="a62c1-167">구성 요소 네임스페이스에 대한 [`@using`][2] 지시문을 추가하면 해당 구성 요소를 사용할 수 있게 되므로 경고가 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="a62c1-168">라우팅</span><span class="sxs-lookup"><span data-stu-id="a62c1-168">Routing</span></span>

<span data-ttu-id="a62c1-169">Blazor의 라우팅은 앱에서 액세스 가능한 각 구성 요소에 경로 템플릿을 제공하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="a62c1-170">[`@page`][9] 지시문을 포함하는 Razor 파일이 컴파일되면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="a62c1-171">런타임에 라우터는 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>를 사용하여 구성 요소 클래스를 검색하고, 요청된 URL과 일치하는 경로 템플릿을 포함하는 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-171">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="a62c1-172">자세한 내용은 <xref:blazor/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="a62c1-173">매개 변수</span><span class="sxs-lookup"><span data-stu-id="a62c1-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="a62c1-174">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="a62c1-174">Route parameters</span></span>

<span data-ttu-id="a62c1-175">구성 요소는 [`@page`][9] 지시문에 제공된 경로 템플릿에서 경로 매개 변수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="a62c1-176">라우터는 경로 매개 변수를 사용하여 해당하는 구성 요소 매개 변수를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="a62c1-177">*Pages/RouteParameter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="a62c1-178">선택적 매개 변수는 지원되지 않으므로 앞의 예제에서 두 개의 [`@page`][9] 지시문이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="a62c1-179">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="a62c1-180">두 번째 [`@page`][9] 지시문은 `{text}` 경로 매개 변수를 받고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="a62c1-181">여러 폴더 경계에서 경로를 캡처하는 *catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소( *.razor*)에서 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="a62c1-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="a62c1-182">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="a62c1-182">Component parameters</span></span>

<span data-ttu-id="a62c1-183">구성 요소에는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 구성 요소 클래스의 퍼블릭 속성을 사용하여 정의되는 구성 요소 매개 변수가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-183">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="a62c1-184">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="a62c1-185">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="a62c1-186">샘플 앱의 다음 예제에서는 `ParentComponent`는 `ChildComponent`의 `Title` 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="a62c1-187">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="a62c1-188">자체 *구성 요소 매개 변수*에 쓰는 구성 요소를 만들지 말고 대신 private 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="a62c1-189">자세한 내용은 [자체 매개 변수 속성에 쓰는 구성 요소 만들지 않음](#dont-create-components-that-write-to-their-own-parameter-properties) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="a62c1-190">자식 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="a62c1-190">Child content</span></span>

<span data-ttu-id="a62c1-191">구성 요소는 다른 구성 요소의 콘텐츠를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-191">Components can set the content of another component.</span></span> <span data-ttu-id="a62c1-192">할당 구성 요소는 받는 구성 요소를 지정하는 태그 간 콘텐츠를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="a62c1-193">다음 예제에서 `ChildComponent`에는 렌더링할 UI의 세그먼트를 나타내는 <xref:Microsoft.AspNetCore.Components.RenderFragment>를 나타내는 `ChildContent` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="a62c1-194">`ChildContent`의 값은 콘텐츠를 렌더링해야 하는 구성 요소의 태그에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="a62c1-195">`ChildContent` 값은 부모 구성 요소에서 수신되고 부트스트랩 패널의 `panel-body` 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="a62c1-196">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="a62c1-197"><xref:Microsoft.AspNetCore.Components.RenderFragment> 콘텐츠를 받는 속성은 규칙에 따라 이름 `ChildContent`가 지정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-197">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="a62c1-198">샘플 앱의 `ParentComponent`는 콘텐츠를 `<ChildComponent>` 태그 안에 배치하여 `ChildComponent`를 렌더링하기 위한 콘텐츠를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="a62c1-199">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="a62c1-200">특성 스플래팅 및 임의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="a62c1-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="a62c1-201">구성 요소는 구성 요소의 선언된 매개 변수 외에, 추가 특성도 캡처하고 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="a62c1-202">추가 특성을 사전에 캡처한 다음, [`@attributes`][3] Razor 지시문을 사용하여 구성 요소를 렌더링할 때 요소에 ‘스플래팅’할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="a62c1-203">이 시나리오는 다양한 사용자 지정을 지원하는 태그 요소를 생성하는 구성 요소를 정의할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="a62c1-204">예를 들어, 많은 매개 변수를 지원하는 `<input>`에 대해 개별적으로 특성을 정의하는 것이 번거로울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="a62c1-205">다음 예제에서 첫 번째 `<input>` 요소(`id="useIndividualParams"`)는 개별 구성 요소 매개 변수를 사용하지만 두 번째 `<input>` 요소(`id="useAttributesDict"`)는 특성 스플래팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="a62c1-206">매개 변수의 형식은 문자열 키를 사용하여 `IEnumerable<KeyValuePair<string, object>>`를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="a62c1-207">이 시나리오에서는 `IReadOnlyDictionary<string, object>`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="a62c1-208">두 방법을 사용하여 렌더링되는 `<input>` 요소는 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-208">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="a62c1-209">임의 특성을 허용하려면 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성이 `true`로 설정된 상태로 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 구성 요소 매개 변수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-209">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="a62c1-210">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)의 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 속성을 사용하면 해당 매개 변수는 다른 매개 변수와 일치하지 않는 모든 특성을 일치시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-210">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="a62c1-211">구성 요소는 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>를 사용하여 단일 매개 변수만 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-211">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="a62c1-212"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>에 사용되는 속성 형식은 문자열 키가 있는 `Dictionary<string, object>`에서 할당할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-212">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="a62c1-213">이 시나리오에서는 `IEnumerable<KeyValuePair<string, object>>` 또는 `IReadOnlyDictionary<string, object>`도 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="a62c1-214">요소 특성의 위치에 상대적인 [`@attributes`][3]의 위치는 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="a62c1-215">요소에 [`@attributes`][3]가 스플래팅되면 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 특성이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="a62c1-216">`Child` 구성 요소를 사용하는 구성 요소의 다음 예를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="a62c1-217">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a62c1-218">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a62c1-219">`Child` 구성 요소의 `extra` 특성은 [`@attributes`][3] 오른쪽으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="a62c1-220">특성은 오른쪽에서 왼쪽으로(마지막에 도달하면 처음으로) 처리되기 때문에 `Parent` 구성 요소의 렌더링된 `<div>`는 추가 특성을 통해 전달될 때 `extra="5"`를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="a62c1-221">다음 예에서는 `Child` 구성 요소의 `<div>`에서 `extra`와 [`@attributes`][3]의 순서가 반대로 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="a62c1-222">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a62c1-223">*ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a62c1-224">`Parent` 구성 요소의 렌더링된 `<div>`에는 추가 특성을 통해 전달될 경우 `extra="10"`을 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="a62c1-225">구성 요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="a62c1-225">Capture references to components</span></span>

<span data-ttu-id="a62c1-226">구성 요소 참조에서는 해당 인스턴스에 대해 명령(예: (`Show` 또는 `Reset`)을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="a62c1-227">구성 요소 참조를 캡처하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-227">To capture a component reference:</span></span>

* <span data-ttu-id="a62c1-228">자식 구성 요소에 [`@ref`][4] 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="a62c1-229">자식 구성 요소와 동일한 유형으로 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-229">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="a62c1-230">구성 요소가 렌더링되면 `loginDialog` 필드가 `MyLoginDialog` 자식 구성 요소 인스턴스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="a62c1-231">그런 다음, 구성 요소 인스턴스에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a62c1-232">`loginDialog` 변수는 구성 요소가 렌더링된 후에만 채워지고 출력에는 `MyLoginDialog` 요소가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="a62c1-233">이 시점까지 참조할 항목이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="a62c1-234">구성 요소에서 렌더링을 완료한 후에 구성 요소 참조를 조작하려면 [OnAfterRenderAsync 또는 OnAfterRender 메서드](xref:blazor/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="a62c1-235">루프의 구성 요소를 참조하려면 [Capture references to multiple similar child-components(dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)(여러 비슷한 자식 구성 요소에 대한 참조 캡처)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="a62c1-236">구성 요소 참조 캡처에는 [요소 참조 캡처](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)와 유사한 구문을 사용하지만 JavaScript interop 기능이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="a62c1-237">구성 요소 참조가 JavaScript 코드로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="a62c1-238">구성 요소 참조는 .NET 코드에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="a62c1-239">구성 요소 참조를 사용하여 자식 구성 요소의 상태를 변경하지 **않도록 합니다**.</span><span class="sxs-lookup"><span data-stu-id="a62c1-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="a62c1-240">대신, 일반 선언적 매개 변수를 사용하여 자식 구성 요소에 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="a62c1-241">일반 선언적 매개 변수를 사용하면 자식 구성 요소가 올바른 시간에 자동으로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="a62c1-242">외부에서 구성 요소 메서드를 호출하여 상태 업데이트</span><span class="sxs-lookup"><span data-stu-id="a62c1-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="a62c1-243">는 동기화 컨텍스트(<xref:System.Threading.SynchronizationContext>)를 사용하여 단일 논리적 실행 스레드를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-243"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="a62c1-244">구성 요소의 [수명 주기 메서드](xref:blazor/lifecycle) 및 Blazor에서 발생하는 모든 이벤트 콜백은 이 동기화 컨텍스트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="a62c1-245"> 서버의 동기화 컨텍스트는 단일 스레드인 브라우저의 WebAssembly 모델과 거의 일치하도록 단일 스레드 환경 에뮬레이션을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-245"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="a62c1-246">지정된 시점에서 작업이 정확히 하나의 스레드에서만 수행되어 단일 논리적 스레드의 느낌을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="a62c1-247">두 작업이 동시에 실행되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="a62c1-248">외부 이벤트(예: 타이머 또는 다른 알림)를 기준으로 구성 요소를 업데이트해야 하는 경우 Blazor의 동기화 컨텍스트에 디스패치되는 `InvokeAsync` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="a62c1-249">예를 들어, 업데이트된 상태를 수신 구성 요소에 알릴 수 있는 *알림 서비스*을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="a62c1-250">`NotifierService`를 싱글톤으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="a62c1-251">Blazor WebAssembly에서 `Program.Main`에 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="a62c1-252">Blazor 서버에서 `Startup.ConfigureServices`에 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="a62c1-253">`NotifierService`를 사용하여 구성 요소를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-253">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="a62c1-254">위의 예제에서 `NotifierService`는 Blazor의 동기화 컨텍스트 외부에서 구성 요소의 `OnNotify` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="a62c1-255">`InvokeAsync`는 올바른 컨텍스트로 전환하고 렌더링을 큐에 대기하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="a62c1-256">\@ 키를 사용하여 요소 및 구성 요소 유지</span><span class="sxs-lookup"><span data-stu-id="a62c1-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="a62c1-257">요소 또는 구성 요소 목록을 렌더링하고, 이후에 요소 또는 구성 요소가 변경되는 경우 Blazor의 Diff 알고리즘은 유지할 수 있는 이전 요소 또는 구성 요소와 모델 개체가 이러한 요소에 매핑되는 방법을 결정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="a62c1-258">일반적으로 이 프로세스는 자동이며 무시해도 되지만 프로세스를 제어하려는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="a62c1-259">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-259">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="a62c1-260">`People` 컬렉션의 콘텐츠는 삽입, 삭제 또는 다시 정렬된 항목으로 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="a62c1-261">구성 요소가 다시 렌더링되면 `<DetailsEditor>` 구성 요소가 다른 `Details` 매개 변수 값을 수신하도록 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="a62c1-262">이로 인해 다시 렌더링이 예상보다 더 복잡해질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="a62c1-263">경우에 따라 다시 렌더링을 수행하면 요소 포커스 손실과 같은 동작 차이가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="a62c1-264">매핑 프로세스는 [`@key`][5] 지시문 특성을 사용하여 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="a62c1-265">[`@key`][5]를 사용하면 diff 알고리즘은 키의 값에 따라 요소 또는 구성 요소가 유지되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="a62c1-266">`People` 컬렉션이 변경되면 diff 알고리즘은 `<DetailsEditor>` 인스턴스와 `person` 인스턴스 간 연결을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="a62c1-267">`People` 목록에서 `Person`이 삭제된 경우 해당 `<DetailsEditor>` 인스턴스만 UI에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="a62c1-268">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a62c1-269">`Person`이 목록의 특정 위치에 삽입되는 경우 해당 위치에 하나의 새 `<DetailsEditor>` 인스턴스가 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="a62c1-270">다른 인스턴스는 변경되지 않은 상태로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a62c1-271">`Person` 항목이 다시 정렬되면 해당 `<DetailsEditor>` 인스턴스가 유지되고 UI에서 다시 정렬됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="a62c1-272">일부 시나리오에서는 [`@key`][5]를 사용하여 재랜더링의 복잡성을 최소화하고, DOM의 상태 저장 부분(예: 포커스 위치)이 변경될 수 있는 잠재적 문제를 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a62c1-273">키는 각 컨테이너 요소 또는 구성 요소에 대해 로컬입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="a62c1-274">문서 전체에서 키가 전역적으로 비교되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="a62c1-275">\@ 키를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="a62c1-275">When to use \@key</span></span>

<span data-ttu-id="a62c1-276">일반적으로 목록이 렌더링될 때마다(예: [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 블록에서) [`@key`][5]를 사용하는 것이 적절하며 [`@key`][5]를 정의하기 위한 적절한 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="a62c1-277">[`@key`][5]를 사용하여 개체가 변경될 때 Blazor가 요소 또는 구성 요소 하위 트리를 유지하지 않도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="a62c1-278">`@currentPerson`이 변경되면 [`@key`][5] 특성 지시문은 Blazor가 강제로 전체 `<div>` 및 해당 하위 항목을 삭제하고 새 요소와 구성 요소를 사용하여 UI 내에서 하위 트리를 다시 빌드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="a62c1-279">이것은 `@currentPerson`이 변경될 때 UI 상태가 유지되지 않도록 해야 하는 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="a62c1-280">\@ 키를 사용하지 않는 경우</span><span class="sxs-lookup"><span data-stu-id="a62c1-280">When not to use \@key</span></span>

<span data-ttu-id="a62c1-281">[`@key`][5]로 diff를 수행할 때 성능 비용이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="a62c1-282">성능 비용은 크지 않지만 요소 또는 구성 요소 유지 규칙을 제어할 때 앱에 도움이 되는 경우에만 [`@key`][5]를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="a62c1-283">[`@key`][5]가 사용되지 않더라도 Blazor는 자식 요소와 구성 요소 인스턴스를 최대한 많이 보존합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="a62c1-284">[`@key`][5]를 사용할 때의 유일한 장점은 매핑을 선택하는 diff 알고리즘 대신, 모델 인스턴스가 유지된 구성 요소 인스턴스에 매핑되는 *방법*을 제어할 수 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="a62c1-285">\@ 키에 사용할 값</span><span class="sxs-lookup"><span data-stu-id="a62c1-285">What values to use for \@key</span></span>

<span data-ttu-id="a62c1-286">일반적으로 [`@key`][5]에 대해 다음과 같은 종류의 값 중 하나를 제공하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="a62c1-287">모델 개체 인스턴스(예: 이전 예제와 같은 `Person` 인스턴스)</span><span class="sxs-lookup"><span data-stu-id="a62c1-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="a62c1-288">이렇게 하면 개체 참조 같음에 따라 보존이 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="a62c1-289">고유 식별자(예: `int`, `string` 또는 `Guid` 형식의 기본 키 값)</span><span class="sxs-lookup"><span data-stu-id="a62c1-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="a62c1-290">[`@key`][5]에 사용되는 값이 충돌하지 않는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="a62c1-291">동일한 부모 요소 내에서 충돌하는 값이 감지되면 이전 요소나 구성 요소를 새 요소나 구성 요소에 확정적으로 매핑할 수 없으므로 Blazor는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="a62c1-292">개체 인스턴스 또는 기본 키 값과 같은 고유 값만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="a62c1-293">자체 매개 변수 속성에 쓰는 구성 요소를 만들지 않음</span><span class="sxs-lookup"><span data-stu-id="a62c1-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="a62c1-294">다음 조건에서는 매개 변수를 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="a62c1-295">자식 구성 요소 콘텐츠가 <xref:Microsoft.AspNetCore.Components.RenderFragment>로 렌더링되는 경우</span><span class="sxs-lookup"><span data-stu-id="a62c1-295">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="a62c1-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 부모 구성 요소에서 호출되는 경우</span><span class="sxs-lookup"><span data-stu-id="a62c1-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="a62c1-297"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출될 때 부모 구성 요소가 렌더링되고 자식 구성 요소에 새 매개 변수 값이 제공되므로 매개 변수가 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="a62c1-298">다음을 수행하는 `Expander` 구성 요소를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="a62c1-299">자식 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-299">Renders child content.</span></span>
* <span data-ttu-id="a62c1-300">구성 요소 매개 변수로 자식 콘텐츠 표시를 설정/해제합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-300">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="a62c1-301">`Expander` 구성 요소는 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출할 수 있는 부모 구성 요소에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-301">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="a62c1-302">처음에 `Expander` 구성 요소는 `Expanded` 속성이 전환될 때 독립적으로 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="a62c1-303">자식 구성 요소는 상태를 예상대로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="a62c1-304">부모에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출되면 첫 번째 자식 구성 요소의 `Expanded` 매개 변수가 초기 값(`true`)으로 다시 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-304">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="a62c1-305">두 번째 `Expander` 구성 요소에서는 렌더링디는 자식 콘텐츠가 없으므로 구성 요소의 `Expanded` 값이 다시 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="a62c1-306">앞의 시나리오에서 상태를 유지하려면 `Expander` 구성 요소에서 ‘private 필드’를 사용하여 전환된 상태를 유지합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="a62c1-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="a62c1-307">다음 `Expander` 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="a62c1-307">The following `Expander` component:</span></span>

* <span data-ttu-id="a62c1-308">부모의 `Expanded` 구성 요소 매개 변수 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="a62c1-309">[OnInitialized 이벤트](xref:blazor/lifecycle#component-initialization-methods)에서 구성 요소 매개 변수 값을 ‘private 필드’(`expanded`)에 할당합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="a62c1-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="a62c1-310">Private 필드를 사용하여 내부 설정/해제 상태를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-310">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="a62c1-311">Partial 클래스 지원</span><span class="sxs-lookup"><span data-stu-id="a62c1-311">Partial class support</span></span>

Razor<span data-ttu-id="a62c1-312"> 구성 요소가 partial 클래스로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="a62c1-313"> 구성 요소는 다음 방법 중 하나를 사용하여 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="a62c1-314">C# 코드는 단일 파일에서 HTML 태그와 Razor 코드를 사용하여 [`@code`][1] 블록에 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="a62c1-315"> 템플릿은 이 접근 방식을 사용하여 Razor 구성 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="a62c1-316">C# 코드는 partial 클래스로 정의된 코드 숨김 파일에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="a62c1-317">다음 예제에서는 Blazor 템플릿에서 생성된 앱에서 [`@code`][1] 블록을 포함하는 기본 `Counter` 구성 요소를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="a62c1-318">HTML 태그, Razor 코드 및 C# 코드는 다음과 같은 동일한 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="a62c1-319">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-319">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="a62c1-320">Partial 클래스에서 코드 숨김 파일을 사용하여 `Counter` 구성 요소를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="a62c1-321">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="a62c1-322">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-322">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="a62c1-323">필요한 경우 partial 클래스 파일에 필요한 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="a62c1-324">Razor 구성 요소에 사용되는 일반적인 네임스페이스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="a62c1-325">기본 클래스 지정</span><span class="sxs-lookup"><span data-stu-id="a62c1-325">Specify a base class</span></span>

<span data-ttu-id="a62c1-326">[`@inherits`][6] 지시어를 사용하여 구성 요소에 대한 기본 클래스를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="a62c1-327">다음 예제에서는 구성 요소가 기본 클래스 `BlazorRocksBase`를 상속하여 구성 요소의 속성과 메서드를 제공하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="a62c1-328">기본 클래스는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-328">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="a62c1-329">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="a62c1-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-330">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a><span data-ttu-id="a62c1-331">특성 지정</span><span class="sxs-lookup"><span data-stu-id="a62c1-331">Specify an attribute</span></span>

<span data-ttu-id="a62c1-332">[`@attribute`][7] 지시문을 사용하여 Razor 구성 요소에 특성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="a62c1-333">다음 예제에서는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 구성 요소 클래스에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-333">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="a62c1-334">구성 요소 가져오기</span><span class="sxs-lookup"><span data-stu-id="a62c1-334">Import components</span></span>

<span data-ttu-id="a62c1-335">Razor로 작성된 구성 요소의 네임스페이스는 다음을 기준으로 합니다(우선 순위에 따름).</span><span class="sxs-lookup"><span data-stu-id="a62c1-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="a62c1-336">Razor 파일( *.razor*) 태그(`@namespace BlazorSample.MyNamespace`)의 [`@namespace`][8] 지정</span><span class="sxs-lookup"><span data-stu-id="a62c1-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="a62c1-337">프로젝트 파일(`<RootNamespace>BlazorSample</RootNamespace>`)에서 프로젝트의 `RootNamespace`</span><span class="sxs-lookup"><span data-stu-id="a62c1-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="a62c1-338">프로젝트 파일의 파일 이름( *.csproj*)에서 가져온 프로젝트 이름, 프로젝트 루트에서 구성 요소로의 경로.</span><span class="sxs-lookup"><span data-stu-id="a62c1-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="a62c1-339">예를 들어, 이 프레임워크는 *{PROJECT ROOT}/Pages/Index.razor*(*BlazorSample.csproj*)를 네임스페이스 `BlazorSample.Pages`로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="a62c1-340">구성 요소는 C# 이름 바인딩 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="a62c1-341">이 예의 `Index` 구성 요소에서는 범위의 구성 요소가 모든 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="a62c1-342">동일한 폴더의 *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a62c1-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="a62c1-343">다른 네임스페이스를 명시적으로 지정하지 않는 프로젝트 루트의 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a62c1-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="a62c1-344">다른 네임스페이스에 정의된 구성 요소는 Razor의 [`@using`][2] 지시문을 사용하여 범위로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="a62c1-345">다른 구성 요소 `NavMenu.razor`가 *BlazorSample/Shared/* 폴더에 있는 경우 다음 [`@using`][2] 문을 사용하여 `Index.razor`에서 해당 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="a62c1-346">구성 요소는 정규화된 이름을 사용하여 참조할 수도 있습니다. 이 경우에는 [`@using`][2] 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="a62c1-347">`global::` 한정자는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="a62c1-348">별칭이 지정된 [using](/dotnet/csharp/language-reference/keywords/using-statement) 문(예: `@using Foo = Bar`)을 사용하여 구성 요소를 가져오는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-348">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="a62c1-349">부분적으로 정규화된 이름은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="a62c1-350">예를 들어 `<Shared.NavMenu></Shared.NavMenu>`를 사용하여 `@using BlazorSample`을 추가하고 `NavMenu` 구성 요소(`NavMenu.razor`)를 참조하는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-350">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="a62c1-351">조건부 HTML 요소 특성</span><span class="sxs-lookup"><span data-stu-id="a62c1-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="a62c1-352">HTML 요소 특성은 .NET 값에 따라 조건부로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="a62c1-353">값이 `false` 또는 `null`이면 특성이 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="a62c1-354">값이 `true`이면 특성이 최소화된 상태로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="a62c1-355">다음 예제에서 `IsCompleted`는 `checked`가 요소의 태그에서 렌더링되는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="a62c1-356">`IsCompleted`가 `true`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="a62c1-357">`IsCompleted`가 `false`이면 확인란이 다음과 같이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="a62c1-358">자세한 내용은 <xref:mvc/views/razor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="a62c1-359">[aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)와 같은 일부 HTML 특성은 .NET 형식이 `bool`일 경우 제대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="a62c1-360">이러한 경우 `bool` 대신 `string` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="a62c1-361">원시 HTML</span><span class="sxs-lookup"><span data-stu-id="a62c1-361">Raw HTML</span></span>

<span data-ttu-id="a62c1-362">일반적으로 문자열은 DOM 텍스트 노드를 사용하여 렌더링됩니다. 즉, 포함될 수 있는 모든 태그는 무시되고 리터럴 텍스트로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="a62c1-363">원시 HTML을 렌더링하려면 HTML 콘텐츠를 `MarkupString` 값으로 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="a62c1-364">값은 HTML 또는 SVG로 구문 분석되고 DOM에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="a62c1-365">신뢰할 수 없는 원본에서 생성된 원시 HTML을 렌더링할 경우 **보안 위험**이 있으므로 피해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="a62c1-366">다음 예제에서는 `MarkupString` 형식을 사용하여 정적 HTML 콘텐츠 블록을 구성 요소의 렌더링된 출력에 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="a62c1-367">연계 값 및 매개 변수</span><span class="sxs-lookup"><span data-stu-id="a62c1-367">Cascading values and parameters</span></span>

<span data-ttu-id="a62c1-368">일부 시나리오에서, 특히 여러 구성 요소 계층이 있는 경우 [구성 요소 매개 변수](#component-parameters)를 사용하여 상위 구성 요소에서 하위 구성 요소로 데이터를 이동하는 것이 불편할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="a62c1-369">연계 값 및 매개 변수는 상위 구성 요소에서 모든 하위 구성 요소에 값을 제공하는 편리한 방법을 제공하여 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="a62c1-370">연계 값 및 매개 변수를 사용하여 구성 요소를 조정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="a62c1-371">테마 예</span><span class="sxs-lookup"><span data-stu-id="a62c1-371">Theme example</span></span>

<span data-ttu-id="a62c1-372">샘플 앱의 다음 예제에서 `ThemeInfo` 클래스는 앱의 지정된 부분 내에 있는 모든 단추가 동일한 스타일을 공유할 수 있도록 구성 요소 계층 구조의 아래로 테마 정보가 흐르도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="a62c1-373">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="a62c1-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="a62c1-374">상위 구성 요소는 연계 값 구성 요소를 사용하여 연계 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="a62c1-375"><xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소는 구성 요소 계층의 하위 트리를 래핑하고 해당 하위 트리 내의 모든 구성 요소에 단일 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-375">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="a62c1-376">예를 들어, 샘플 앱은 테마 정보(`ThemeInfo`)를 앱 레이아웃 중 하나에 `@Body` 속성의 레이아웃 본문을 구성하는 모든 구성 요소에 대한 연계 매개 변수로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="a62c1-377">레이아웃 구성 요소에서 `ButtonClass`에는 `btn-success` 값이 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="a62c1-378">모든 하위 구성 요소는 `ThemeInfo` 연계 개체를 통해 이 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="a62c1-379">`CascadingValuesParametersLayout` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="a62c1-379">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="a62c1-380">연계 값을 사용하기 위해 구성 요소는 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 특성을 사용하여 연계 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-380">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="a62c1-381">연계 값은 형식별로 연계 매개 변수에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="a62c1-382">샘플 앱에서 `CascadingValuesParametersTheme` 구성 요소는 `ThemeInfo` 연계 값을 연계 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="a62c1-383">이 매개 변수는 구성 요소에 의해 표시되는 단추 중 하나에 대해 CSS 클래스를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="a62c1-384">`CascadingValuesParametersTheme` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="a62c1-384">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="a62c1-385">동일한 형식의 여러 값을 동일한 하위 트리 내에서 연계하려면 각 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소와 해당 [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 특성에 고유한 <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-385">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="a62c1-386">다음 예제에서는 두 개의 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> 구성 요소가 이름별로 다른 `MyCascadingType` 인스턴스를 연계합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-386">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="a62c1-387">하위 구성 요소에서 연계 매개 변수는 상위 구성 요소의 해당 연계 값에서 해당 값을 이름별로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="a62c1-388">TabSet 예제</span><span class="sxs-lookup"><span data-stu-id="a62c1-388">TabSet example</span></span>

<span data-ttu-id="a62c1-389">연계 매개 변수를 사용하면 여러 구성 요소가 구성 요소 계층 구조 전체에서 공동으로 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="a62c1-390">예를 들어, 샘플 앱에서 다음 *TabSet* 예제를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="a62c1-391">샘플 앱에는 탭이 구현하는 `ITab` 인터페이스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="a62c1-392">`CascadingValuesParametersTabSet` 구성 요소는 여러 `Tab` 구성 요소를 포함하는 `TabSet` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="a62c1-393">자식 `Tab` 구성 요소는 `TabSet`에 대한 매개 변수로 명시적으로 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="a62c1-394">대신, 자식 `Tab` 구성 요소는 `TabSet`의 자식 콘텐츠에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="a62c1-395">그러나 `TabSet`은 헤더 및 활성 탭을 렌더링할 수 있도록 각 `Tab` 구성 요소에 대한 정보를 계속 알고 있어야 합니다. 추가 코드를 요구하지 않고 이러한 조정을 사용하도록 설정하기 위해 `TabSet` 구성 요소는 연계 값으로 제공된 다음, 하위 `Tab` 구성 요소에서 선택될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="a62c1-396">`TabSet` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="a62c1-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="a62c1-397">하위 `Tab` 구성 요소는 포함하는 `TabSet`을 연계 매개 변수로 캡처하므로 `Tab` 구성 요소는 `TabSet`에 추가되고 활성 상태인 탭을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="a62c1-398">`Tab` 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="a62c1-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="a62c1-399"> 템플릿</span><span class="sxs-lookup"><span data-stu-id="a62c1-399"> templates</span></span>

<span data-ttu-id="a62c1-400">렌더링 조각은 Razor 템플릿 구문을 사용하여 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-400">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="a62c1-401"> 템플릿은 UI 코드 조각을 정의하는 방법으로, 다음 형식을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-401"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="a62c1-402">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.RenderFragment> 및 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 값을 지정하고 구성 요소에서 직접 템플릿을 렌더링하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-402">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="a62c1-403">렌더링 조각은 [템플릿 구성 요소](xref:blazor/templated-components)에 인수로 전달될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="a62c1-404">이전 코드의 렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="a62c1-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="a62c1-405">SVG(Scalable Vector Graphics) 이미지</span><span class="sxs-lookup"><span data-stu-id="a62c1-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="a62c1-406">Blazor는 HTML을 렌더링하므로 SVG(Scalable Vector Graphics) 이미지( *.svg*)를 포함하는 브라우저 지원 이미지는 `<img>` 태그를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="a62c1-407">마찬가지로, 스타일시트 파일( *.css*)의 CSS 규칙에서는 SVG 이미지가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="a62c1-408">그러나 인라인 SVG 태그는 일부 시나리오에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="a62c1-409">`<svg>` 태그를 구성 요소 파일(*razor*)에 직접 배치하면 기본 이미지 렌더링이 지원되지만 많은 고급 시나리오는 아직 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="a62c1-410">예를 들어, `<use>` 태그는 현재 적용되지 않으며 [`@bind`][10]를 일부 SVG 태그에서는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-410">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="a62c1-411">자세한 내용은 [Blazor(dotnet/aspnetcore #18271)에서 SVG 지원](https://github.com/dotnet/aspnetcore/issues/18271)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a62c1-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a62c1-412">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a62c1-412">Additional resources</span></span>

* <span data-ttu-id="a62c1-413"><xref:security/blazor/server/threat-mitigation>: 리소스를 소모하지 않도록 하는 Blazor 서버 앱을 빌드하는 방법에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a62c1-413"><xref:security/blazor/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
