---
<span data-ttu-id="3f523-101">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-103">'Blazor'</span></span>
- <span data-ttu-id="3f523-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-104">'Identity'</span></span>
- <span data-ttu-id="3f523-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-106">'Razor'</span></span>
- <span data-ttu-id="3f523-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="3f523-108">Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="3f523-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="3f523-109">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3f523-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="3f523-110"> Pages 및 MVC 앱에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="3f523-111">페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="3f523-112">[앱을 준비](#prepare-the-app)한 후 앱 요구 사항에 따라 다음 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="3f523-113">라우팅 가능한 구성 요소: 사용자 요청에서 직접 라우팅할 수 있는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="3f523-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="3f523-114">방문자가 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 구성 요소에 대한 HTTP 요청을 브라우저에서 만들 수 있는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="3f523-115">[Razor Pages 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="3f523-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="3f523-116">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="3f523-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="3f523-117">[페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view): 사용자 요청에서 직접 라우팅할 수 없는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="3f523-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="3f523-118">[구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 앱이 구성 요소를 기존 페이지와 뷰에 포함하는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="3f523-119">앱 준비</span><span class="sxs-lookup"><span data-stu-id="3f523-119">Prepare the app</span></span>

<span data-ttu-id="3f523-120">기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="3f523-121">앱의 레이아웃 파일( *_Layout.cshtml*)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="3f523-122">`<head>` 요소에 다음 `<base>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="3f523-123">위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="3f523-124">앱이 하위 애플리케이션인 경우, <xref:host-and-deploy/blazor/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="3f523-125">*_Layout.cshtml* 파일은 Razor Pages 앱의 *Pages/Shared* 폴더 또는 MVC 앱의 *Views/Shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="3f523-126">닫는 `</body>` 태그 바로 앞에 *blazor.server.js* 스크립트의 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="3f523-127">프레임워크가 *blazor.server.js* 스크립트를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="3f523-128">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="3f523-129">다음 콘텐츠를 사용하여 프로젝트의 루트 폴더에 *_Imports.razor* 파일을 추가합니다(마지막 네임스페이스인 `MyAppNamespace`를 앱의 네임스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="3f523-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="3f523-130">`Startup.ConfigureServices`에서 Blazor Server 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="3f523-131">`Startup.Configure`에서 `app.UseEndpoints`에 Blazor 허브 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="3f523-132">페이지 또는 뷰에 구성 요소를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-132">Integrate components into any page or view.</span></span> <span data-ttu-id="3f523-133">자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="3f523-134">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="3f523-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="3f523-135">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="3f523-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="3f523-136">Razor Pages 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="3f523-137">[앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="3f523-138">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-138">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="3f523-139">다음 콘텐츠를 사용하여 *Pages* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="3f523-140">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="3f523-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="3f523-142">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="3f523-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="3f523-143">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="3f523-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="3f523-144">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="3f523-144">Render Mode</span></span> | <span data-ttu-id="3f523-145">설명</span><span class="sxs-lookup"><span data-stu-id="3f523-145">Description</span></span> |
   | ---
<span data-ttu-id="3f523-146">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-148">'Blazor'</span></span>
- <span data-ttu-id="3f523-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-149">'Identity'</span></span>
- <span data-ttu-id="3f523-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-151">'Razor'</span></span>
- <span data-ttu-id="3f523-152">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-153">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-155">'Blazor'</span></span>
- <span data-ttu-id="3f523-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-156">'Identity'</span></span>
- <span data-ttu-id="3f523-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-158">'Razor'</span></span>
- <span data-ttu-id="3f523-159">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-160">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-162">'Blazor'</span></span>
- <span data-ttu-id="3f523-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-163">'Identity'</span></span>
- <span data-ttu-id="3f523-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-165">'Razor'</span></span>
- <span data-ttu-id="3f523-166">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-166">'SignalR' uid:</span></span> 

<span data-ttu-id="3f523-167">------ | --- title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-169">'Blazor'</span></span>
- <span data-ttu-id="3f523-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-170">'Identity'</span></span>
- <span data-ttu-id="3f523-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-172">'Razor'</span></span>
- <span data-ttu-id="3f523-173">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-174">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-176">'Blazor'</span></span>
- <span data-ttu-id="3f523-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-177">'Identity'</span></span>
- <span data-ttu-id="3f523-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-179">'Razor'</span></span>
- <span data-ttu-id="3f523-180">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-181">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-183">'Blazor'</span></span>
- <span data-ttu-id="3f523-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-184">'Identity'</span></span>
- <span data-ttu-id="3f523-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-186">'Razor'</span></span>
- <span data-ttu-id="3f523-187">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-187">'SignalR' uid:</span></span> 

<span data-ttu-id="3f523-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3f523-189">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="3f523-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3f523-191">`App` 구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="3f523-192">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="3f523-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` 구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="3f523-194">구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="3f523-195">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 페이지의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="3f523-196">라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-196">Add routable components to the app.</span></span> <span data-ttu-id="3f523-197">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="3f523-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="3f523-198">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="3f523-199">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="3f523-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="3f523-200">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="3f523-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="3f523-201">MVC 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="3f523-202">[앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="3f523-203">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="3f523-204">다음 콘텐츠를 사용하여 *Views/Home* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="3f523-205">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="3f523-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="3f523-207">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="3f523-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="3f523-208">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="3f523-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="3f523-209">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="3f523-209">Render Mode</span></span> | <span data-ttu-id="3f523-210">설명</span><span class="sxs-lookup"><span data-stu-id="3f523-210">Description</span></span> |
   | ---
<span data-ttu-id="3f523-211">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-213">'Blazor'</span></span>
- <span data-ttu-id="3f523-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-214">'Identity'</span></span>
- <span data-ttu-id="3f523-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-216">'Razor'</span></span>
- <span data-ttu-id="3f523-217">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-218">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-220">'Blazor'</span></span>
- <span data-ttu-id="3f523-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-221">'Identity'</span></span>
- <span data-ttu-id="3f523-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-223">'Razor'</span></span>
- <span data-ttu-id="3f523-224">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-225">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-227">'Blazor'</span></span>
- <span data-ttu-id="3f523-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-228">'Identity'</span></span>
- <span data-ttu-id="3f523-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-230">'Razor'</span></span>
- <span data-ttu-id="3f523-231">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-231">'SignalR' uid:</span></span> 

<span data-ttu-id="3f523-232">------ | --- title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-234">'Blazor'</span></span>
- <span data-ttu-id="3f523-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-235">'Identity'</span></span>
- <span data-ttu-id="3f523-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-237">'Razor'</span></span>
- <span data-ttu-id="3f523-238">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-239">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-241">'Blazor'</span></span>
- <span data-ttu-id="3f523-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-242">'Identity'</span></span>
- <span data-ttu-id="3f523-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-244">'Razor'</span></span>
- <span data-ttu-id="3f523-245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3f523-246">title: 'Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합' author: description: 'Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="3f523-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="3f523-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3f523-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f523-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f523-248">'Blazor'</span></span>
- <span data-ttu-id="3f523-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f523-249">'Identity'</span></span>
- <span data-ttu-id="3f523-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f523-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f523-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f523-251">'Razor'</span></span>
- <span data-ttu-id="3f523-252">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3f523-252">'SignalR' uid:</span></span> 

<span data-ttu-id="3f523-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3f523-254">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="3f523-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3f523-256">`App` 구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="3f523-257">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="3f523-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` 구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="3f523-259">구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="3f523-260">홈 컨트롤러에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="3f523-261">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 뷰를 반환하는 컨트롤러 작업의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="3f523-262">*Pages* 폴더를 만들고 라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="3f523-263">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="3f523-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="3f523-264">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="3f523-265">페이지 또는 뷰에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="3f523-265">Render components from a page or view</span></span>

<span data-ttu-id="3f523-266">‘이 섹션에서는 사용자 요청에서 직접 구성 요소를 라우팅할 수 없는 페이지 또는 뷰에 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="3f523-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="3f523-267">페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="3f523-268">상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="3f523-268">Render stateful interactive components</span></span>

<span data-ttu-id="3f523-269">Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="3f523-270">페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-270">When the page or view renders:</span></span>

* <span data-ttu-id="3f523-271">구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="3f523-272">미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="3f523-273">SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="3f523-274">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="3f523-275">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="3f523-276">비대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="3f523-276">Render noninteractive components</span></span>

<span data-ttu-id="3f523-277">다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="3f523-278">구성 요소가 정적으로 렌더링되므로 구성 요소는 대화형이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-278">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="3f523-279">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="3f523-280">구성 요소 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="3f523-280">Component namespaces</span></span>

<span data-ttu-id="3f523-281">사용자 지정 폴더를 사용하여 앱의 구성 요소를 저장하는 경우, 폴더를 나타내는 네임스페이스를 페이지/뷰 또는 *_ViewImports.cshtml* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="3f523-282">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="3f523-282">In the following example:</span></span>

* <span data-ttu-id="3f523-283">`MyAppNamespace`를 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="3f523-284">구성 요소를 저장하는 데 *Components* 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="3f523-285">*_ViewImports.cshtml* 파일은 Razor Pages 앱의 *Pages* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f523-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="3f523-286">자세한 내용은 <xref:blazor/components#import-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f523-286">For more information, see <xref:blazor/components#import-components>.</span></span>
