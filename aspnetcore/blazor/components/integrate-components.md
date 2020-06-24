---
title: Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 시나리오를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: 78d524bc0271fd2640302bb0de78571ab688bef5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103372"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="5f2d1-103">Razor Pages 및 MVC 앱에 ASP.NET Core Razor 구성 요소 통합</span><span class="sxs-lookup"><span data-stu-id="5f2d1-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="5f2d1-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5f2d1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="5f2d1-105"> Pages 및 MVC 앱에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-105"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="5f2d1-106">페이지나 뷰를 렌더링할 때 구성 요소를 동시에 미리 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="5f2d1-107">[앱을 준비](#prepare-the-app)한 후 앱 요구 사항에 따라 다음 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="5f2d1-108">라우팅 가능한 구성 요소: 사용자 요청에서 직접 라우팅할 수 있는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="5f2d1-109">방문자가 [`@page`](xref:mvc/views/razor#page) 지시문을 사용하여 구성 요소에 대한 HTTP 요청을 브라우저에서 만들 수 있는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="5f2d1-110">[Razor Pages 앱에서 라우팅 가능한 구성 요소 사용](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="5f2d1-110">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="5f2d1-111">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5f2d1-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="5f2d1-112">[페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view): 사용자 요청에서 직접 라우팅할 수 없는 구성 요소.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="5f2d1-113">[구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용하여 앱이 구성 요소를 기존 페이지와 뷰에 포함하는 경우 이 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="5f2d1-114">앱 준비</span><span class="sxs-lookup"><span data-stu-id="5f2d1-114">Prepare the app</span></span>

<span data-ttu-id="5f2d1-115">기존 Razor Pages 또는 MVC 앱은 페이지와 뷰에 Razor 구성 요소를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="5f2d1-116">앱의 레이아웃 파일( *_Layout.cshtml*)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="5f2d1-117">`<head>` 요소에 다음 `<base>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="5f2d1-118">위의 예제에서 `href` 값(‘앱 기본 경로’)은 앱이 루트 URL 경로(`/`)에 있는 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="5f2d1-119">앱이 하위 애플리케이션인 경우, <xref:blazor/host-and-deploy/index#app-base-path> 문서의 ‘앱 기본 경로’ 섹션에 설명된 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="5f2d1-120">*_Layout.cshtml* 파일은 Razor Pages 앱의 *Pages/Shared* 폴더 또는 MVC 앱의 *Views/Shared* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="5f2d1-121">닫는 `</body>` 태그 바로 앞에 *blazor.server.js* 스크립트의 `<script>` 태그를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="5f2d1-122">프레임워크가 *blazor.server.js* 스크립트를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="5f2d1-123">앱에 스크립트를 수동으로 추가할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="5f2d1-124">다음 콘텐츠를 사용하여 프로젝트의 루트 폴더에 *_Imports.razor* 파일을 추가합니다(마지막 네임스페이스인 `MyAppNamespace`를 앱의 네임스페이스로 변경).</span><span class="sxs-lookup"><span data-stu-id="5f2d1-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="5f2d1-125">`Startup.ConfigureServices`에서 Blazor Server 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="5f2d1-126">`Startup.Configure`에서 `app.UseEndpoints`에 Blazor 허브 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="5f2d1-127">페이지 또는 뷰에 구성 요소를 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-127">Integrate components into any page or view.</span></span> <span data-ttu-id="5f2d1-128">자세한 내용은 [페이지 또는 뷰에서 구성 요소 렌더링](#render-components-from-a-page-or-view) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="5f2d1-129">Razor Pages 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5f2d1-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="5f2d1-130">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="5f2d1-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5f2d1-131">Razor Pages 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="5f2d1-132">[앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5f2d1-133">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-133">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="5f2d1-134">다음 콘텐츠를 사용하여 *Pages* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5f2d1-135">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="5f2d1-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5f2d1-137">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="5f2d1-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5f2d1-138">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="5f2d1-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5f2d1-139">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="5f2d1-139">Render Mode</span></span> | <span data-ttu-id="5f2d1-140">설명</span><span class="sxs-lookup"><span data-stu-id="5f2d1-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5f2d1-141">`App` 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5f2d1-142">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5f2d1-143">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5f2d1-144">`App` 구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5f2d1-145">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5f2d1-146">`App` 구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5f2d1-147">구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5f2d1-148">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 페이지의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="5f2d1-149">라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-149">Add routable components to the app.</span></span> <span data-ttu-id="5f2d1-150">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5f2d1-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5f2d1-151">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="5f2d1-152">MVC 앱에서 라우팅 가능한 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="5f2d1-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="5f2d1-153">‘이 섹션에서는 사용자 요청에서 직접 라우팅할 수 있는 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="5f2d1-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="5f2d1-154">MVC 앱에서 라우팅 가능한 Razor 구성 요소를 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="5f2d1-155">[앱 준비](#prepare-the-app) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="5f2d1-156">다음 콘텐츠를 사용하여 프로젝트 루트에 *App.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="5f2d1-157">다음 콘텐츠를 사용하여 *Views/Home* 폴더에 *_Host.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="5f2d1-158">구성 요소는 해당 레이아웃에 공유 *_Layout.cshtml* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="5f2d1-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 `App` 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="5f2d1-160">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="5f2d1-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="5f2d1-161">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="5f2d1-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="5f2d1-162">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="5f2d1-162">Render Mode</span></span> | <span data-ttu-id="5f2d1-163">설명</span><span class="sxs-lookup"><span data-stu-id="5f2d1-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5f2d1-164">`App` 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5f2d1-165">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5f2d1-166">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5f2d1-167">`App` 구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="5f2d1-168">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5f2d1-169">`App` 구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="5f2d1-170">구성 요소 태그 도우미에 대한 자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="5f2d1-171">홈 컨트롤러에 작업을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="5f2d1-172">`Startup.Configure`의 엔드포인트 구성에 *_Host.cshtml* 뷰를 반환하는 컨트롤러 작업의 우선순위가 낮은 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="5f2d1-173">*Pages* 폴더를 만들고 라우팅 가능한 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="5f2d1-174">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5f2d1-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="5f2d1-175">네임스페이스에 대한 자세한 내용은 [구성 요소 네임스페이스](#component-namespaces) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="5f2d1-176">페이지 또는 뷰에서 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="5f2d1-176">Render components from a page or view</span></span>

<span data-ttu-id="5f2d1-177">‘이 섹션에서는 사용자 요청에서 직접 구성 요소를 라우팅할 수 없는 페이지 또는 뷰에 구성 요소를 추가하는 방법을 설명합니다.’</span><span class="sxs-lookup"><span data-stu-id="5f2d1-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="5f2d1-178">페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="5f2d1-179">상태 저장 대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="5f2d1-179">Render stateful interactive components</span></span>

<span data-ttu-id="5f2d1-180">Razor 페이지 또는 뷰에 상태 저장 대화형 구성 요소를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="5f2d1-181">페이지 또는 뷰를 렌더링하는 경우와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-181">When the page or view renders:</span></span>

* <span data-ttu-id="5f2d1-182">구성 요소가 페이지 또는 뷰와 함께 미리 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="5f2d1-183">미리 렌더링하는 데 사용된 초기 구성 요소 상태가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="5f2d1-184">SignalR 연결이 완료되면 새 구성 요소 상태가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="5f2d1-185">다음 Razor 페이지는 `Counter` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="5f2d1-186">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="5f2d1-187">비대화형 구성 요소 렌더링</span><span class="sxs-lookup"><span data-stu-id="5f2d1-187">Render noninteractive components</span></span>

<span data-ttu-id="5f2d1-188">다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 통해 지정된 초기 값을 사용하여 정적으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="5f2d1-189">구성 요소가 정적으로 렌더링되므로 구성 요소는 대화형이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="5f2d1-190">자세한 내용은 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="5f2d1-191">구성 요소 네임스페이스</span><span class="sxs-lookup"><span data-stu-id="5f2d1-191">Component namespaces</span></span>

<span data-ttu-id="5f2d1-192">사용자 지정 폴더를 사용하여 앱의 구성 요소를 저장하는 경우, 폴더를 나타내는 네임스페이스를 페이지/뷰 또는 *_ViewImports.cshtml* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="5f2d1-193">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="5f2d1-193">In the following example:</span></span>

* <span data-ttu-id="5f2d1-194">`MyAppNamespace`를 앱의 네임스페이스로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="5f2d1-195">구성 요소를 저장하는 데 *Components* 폴더를 사용하지 않은 경우, `Components`를 구성 요소가 있는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="5f2d1-196">*_ViewImports.cshtml* 파일은 Razor Pages 앱의 *Pages* 폴더 또는 MVC 앱의 *Views* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-196">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="5f2d1-197">자세한 내용은 <xref:blazor/components/index#namespaces>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5f2d1-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>