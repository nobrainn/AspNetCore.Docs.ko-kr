---
title: Blazor 템플릿 ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor 앱 템플릿 및 Blazor 프로젝트 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: e82f28afdac8517f72538094d97f28bdcfe46102
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74551536"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="b28fb-103">Blazor 템플릿 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b28fb-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="b28fb-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b28fb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b28fb-105">Blazor 프레임 워크는 각 Blazor 호스팅 모델에 대해 앱을 개발 하는 템플릿을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="b28fb-106"> Weasembmbse(`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="b28fb-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="b28fb-107"> 서버 (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="b28fb-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="b28fb-108">Blazor의 호스팅 모델에 대 한 자세한 내용은 <xref:blazor/hosting-models>을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b28fb-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="b28fb-109">템플릿에서 Blazor 앱을 만드는 방법에 대 한 단계별 지침은 <xref:blazor/get-started>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b28fb-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="b28fb-110"> 프로젝트 구조</span><span class="sxs-lookup"><span data-stu-id="b28fb-110"> project structure</span></span>

<span data-ttu-id="b28fb-111">Blazor 템플릿에서 생성 된 Blazor 앱을 구성 하는 파일 및 폴더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="b28fb-112">*Program.cs* &ndash; ASP.NET Core [호스트](xref:fundamentals/host/generic-host)를 설정 하는 앱의 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-112">*Program.cs* &ndash; The app's entry point that sets up the ASP.NET Core [host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="b28fb-113">이 파일의 코드는 ASP.NET Core 템플릿에서 생성 된 모든 ASP.NET Core 앱에 공통적입니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-113">The code in this file is common to all ASP.NET Core apps generated from ASP.NET Core templates.</span></span>

* <span data-ttu-id="b28fb-114">*Startup.cs* &ndash;는 앱의 시작 논리를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-114">*Startup.cs* &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="b28fb-115">`Startup` 클래스는 두 가지 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-115">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="b28fb-116">`ConfigureServices` &ndash;는 응용 프로그램의 [DI (종속성 주입)](xref:fundamentals/dependency-injection) 서비스를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-116">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="b28fb-117">Blazor Server 앱에서 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>를 호출 하 여 서비스를 추가 하 고 `WeatherForecastService` 예제 `FetchData` 구성 요소에서 사용할 수 있도록 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-117">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="b28fb-118">`Configure` &ndash;는 앱의 요청 처리 파이프라인을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-118">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * Blazor<span data-ttu-id="b28fb-119"> Weasembom&ndash;는 앱의 루트 구성 요소인 `App` 구성 요소 (`app` DOM 요소로 지정 됨)를 `AddComponent` 메서드에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-119"> WebAssembly &ndash; Adds the `App` component (specified as the `app` DOM element to the `AddComponent` method), which is the root component of the app.</span></span>
    * Blazor<span data-ttu-id="b28fb-120"> 서버</span><span class="sxs-lookup"><span data-stu-id="b28fb-120"> Server</span></span>
      * <span data-ttu-id="b28fb-121">브라우저를 사용 하 여 실시간 연결에 대 한 끝점을 설정 하기 위해 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-121"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="b28fb-122">연결은 응용 프로그램에 실시간 웹 기능을 추가 하기 위한 프레임 워크인 [SignalR](xref:signalr/introduction)를 사용 하 여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-122">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
      * <span data-ttu-id="b28fb-123">[MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 는 앱의 루트 페이지 (*Pages/_Host*)를 설정 하 고 탐색을 활성화 하기 위해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-123">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="b28fb-124">*wwwroot/index.html* (Blazor Weasembmbomembamboma)는 html 페이지로 구현 된 앱의 루트 페이지를 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-124">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="b28fb-125">앱의 페이지를 처음 요청 하면이 페이지가 렌더링 되어 응답으로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-125">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="b28fb-126">페이지는 루트 `App` 구성 요소가 렌더링 되는 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-126">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="b28fb-127">`App` 구성 요소 (*응용 프로그램 razor*)는 `Startup.Configure`의 `AddComponent` 메서드에 `app` DOM 요소로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-127">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="b28fb-128">*_Framework/Blazor.webassembly.js* JavaScript 파일이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-128">The *_framework/blazor.webassembly.js* JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="b28fb-129">.NET 런타임, 앱 및 앱의 종속성을 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-129">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="b28fb-130">응용 프로그램을 실행 하도록 런타임을 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-130">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="b28fb-131">*Pages/_Host* (Blazor Server)는 Razor 페이지로 구현 된 앱의 루트 페이지를 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-131">*Pages/_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
  * <span data-ttu-id="b28fb-132">앱의 페이지를 처음 요청 하면이 페이지가 렌더링 되어 응답으로 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-132">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="b28fb-133">*_Framework/Blazor.server.js* JavaScript 파일이 로드 되며, 브라우저와 서버 간에 실시간 SignalR 연결을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-133">The *_framework/blazor.server.js* JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
  * <span data-ttu-id="b28fb-134">호스트 페이지는 루트 `App` 구성 요소 (*응용 프로그램 razor*)가 렌더링 되는 위치를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-134">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>

* <span data-ttu-id="b28fb-135">*응용 프로그램 razor* 는 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용 하 여 클라이언트 쪽 라우팅을 설정 하는 앱의 루트 구성 요소를 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-135">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="b28fb-136">`Router` 구성 요소는 브라우저 탐색을 가로채서 요청 된 주소와 일치 하는 페이지를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-136">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="b28fb-137">*페이지* 폴더 &ndash; Blazor 앱을 구성 하는 라우팅 가능 구성 요소/페이지 (*Razor*)를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-137">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app.</span></span> <span data-ttu-id="b28fb-138">각 페이지에 대 한 경로는 [@page](xref:mvc/views/razor#page) 지시어를 사용 하 여 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-138">The route for each page is specified using the [@page](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="b28fb-139">템플릿에는 다음 구성 요소가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-139">The template includes the following components:</span></span>
  * <span data-ttu-id="b28fb-140">홈 페이지를 구현 하는 `Index` (*인덱스 razor*) &ndash;입니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-140">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>
  * <span data-ttu-id="b28fb-141">카운터 페이지를 구현 하는 *`Counter` (&ndash;)입니다.*</span><span class="sxs-lookup"><span data-stu-id="b28fb-141">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="b28fb-142">`Error` (*오류. razor*, Blazor Server 앱에만 해당) 앱에서 처리 되지 않은 예외가 발생 하면 렌더링 &ndash;.</span><span class="sxs-lookup"><span data-stu-id="b28fb-142">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="b28fb-143">`FetchData` (*fetchdata. razor*) &ndash; Fetch data 페이지를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-143">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>

* <span data-ttu-id="b28fb-144">*공유* 폴더 &ndash;는 앱에서 사용 하는 다른 UI 구성 요소 (razor)를 포함*합니다*.</span><span class="sxs-lookup"><span data-stu-id="b28fb-144">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="b28fb-145">`MainLayout` (*mainlayout. razor*)는 앱의 레이아웃 구성 요소 &ndash; 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-145">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="b28fb-146">`NavMenu` (*NavMenu*) &ndash;는 사이드바 탐색을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-146">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="b28fb-147">다른 Razor 구성 요소에 대 한 탐색 링크를 렌더링 하는 [NavLink 구성 요소](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-147">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="b28fb-148">구성 요소가 로드 될 때 `NavLink` 구성 요소가 자동으로 선택 된 상태를 표시 하므로 사용자가 현재 표시 되는 구성 요소를 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-148">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="b28fb-149">*_Imports razor* &ndash;에는 네임 스페이스에 대 한 [@using](xref:mvc/views/razor#using) 지시문과 같은 응용 프로그램의 구성 요소 (*Razor*)에 포함할 일반적인 razor 지시문이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-149">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [@using](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="b28fb-150">*데이터* 폴더 (Blazor 서버) &ndash;에는 응용 프로그램의 `FetchData` 구성 요소에 예제 날씨 데이터를 제공 하는 `WeatherForecastService`의 `WeatherForecast` 클래스 및 구현이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-150">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="b28fb-151">*wwwroot* &ndash; 앱의 공용 정적 자산이 포함 된 앱의 [웹 루트](xref:fundamentals/index#web-root) 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-151">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="b28fb-152">*appsettings* (Blazor Server) &ndash; 앱에 대 한 구성 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="b28fb-152">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>