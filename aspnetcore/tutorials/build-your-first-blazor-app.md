---
title: 첫 번째 Blazor 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292778"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="45a31-103">첫 번째 Blazor 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="45a31-103">Build your first Blazor app</span></span>

<span data-ttu-id="45a31-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="45a31-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45a31-105">이 자습서에서는 Blazor 앱을 만들고 수정하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="45a31-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45a31-107">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="45a31-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="45a31-108">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="45a31-108">Modify Razor components</span></span>
> * <span data-ttu-id="45a31-109">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="45a31-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="45a31-110">Blazor 앱에서 DI(종속성 주입) 및 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="45a31-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="45a31-111">이 자습서의 내용을 마치면 할 일 목록 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="45a31-112">구성 요소 빌드</span><span class="sxs-lookup"><span data-stu-id="45a31-112">Build components</span></span>

1. <span data-ttu-id="45a31-113"><xref:blazor/get-started> 문서의 지침에 따라 이 자습서에 사용할 Blazor 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="45a31-114">프로젝트 이름을 `ToDoList`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="45a31-115">`Pages` 폴더에 있는 앱의 3개 페이지 `Home`, `Counter` 및 `Fetch data`로 각각 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="45a31-116">이러한 페이지는 Razor 구성 요소 파일인 `Index.razor`, `Counter.razor` 및 `FetchData.razor`로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="45a31-117">`Counter` 페이지에서 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="45a31-118">웹 페이지에서 카운터를 증가시키려면 일반적으로 JavaScript를 작성해야 하지만,</span><span class="sxs-lookup"><span data-stu-id="45a31-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="45a31-119">Blazor를 사용하여 C#를 대신 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="45a31-120">`Counter.razor` 파일에서 `Counter` 구성 요소의 구현에 대해 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="45a31-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="45a31-122">`Counter` 구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="45a31-123">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라고 하는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="45a31-124">HTML 태그 및 C# 렌더링 논리는 빌드 시 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="45a31-125">생성되는 .NET 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="45a31-126">구성 요소 클래스의 멤버는 `@code` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="45a31-127">`@code` 블록에서 구성 요소 상태(속성, 필드) 및 메서드가 이벤트 처리 또는 다른 구성 요소 논리 정의를 위해 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="45a31-128">그런 다음, 이러한 멤버를 구성 요소 렌더링 논리의 일부 및 이벤트 처리에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="45a31-129">카운터 증분 단추를 선택하는 경우:</span><span class="sxs-lookup"><span data-stu-id="45a31-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="45a31-130">`Counter` 구성 요소의 등록된 `onclick` 처리기가 호출됩니다(`IncrementCount` 메서드).</span><span class="sxs-lookup"><span data-stu-id="45a31-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="45a31-131">`Counter` 구성 요소가 렌더 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="45a31-132">새 렌더 트리를 이전 렌더 트리와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="45a31-133">DOM(문서 개체 모델)에 대한 수정 내용만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="45a31-134">표시된 개수가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="45a31-135">`Counter` 구성 요소의 C# 논리를 수정하여 카운트를 하나 대신 둘씩 증가하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="45a31-136">앱을 다시 빌드하고 실행하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="45a31-137">단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-137">Select the button.</span></span> <span data-ttu-id="45a31-138">카운터가 2씩 증가됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="45a31-139">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="45a31-139">Use components</span></span>

<span data-ttu-id="45a31-140">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="45a31-141">`Index` 구성 요소(`Index.razor`)에 `<Counter />` 요소를 추가하여 `Counter` 구성 요소를 앱의 `Index` 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="45a31-142">이 자습서의 환경으로 Blazor WebAssembly를 사용하고 있다면 `Index` 구성 요소에 `SurveyPrompt` 구성 요소가 사용되고 있을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="45a31-143">해당 `<SurveyPrompt>` 요소를 `<Counter />` 요소로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="45a31-144">이 자습서의 환경으로 Blazor 서버 앱을 사용하고 있다면 `Index` 구성 요소에 `<Counter />` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="45a31-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="45a31-146">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-146">Rebuild and run the app.</span></span> <span data-ttu-id="45a31-147">`Index` 구성 요소에 자체 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="45a31-148">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="45a31-148">Component parameters</span></span>

<span data-ttu-id="45a31-149">구성 요소는 매개 변수를 포함할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-149">Components can also have parameters.</span></span> <span data-ttu-id="45a31-150">구성 요소 매개 변수는 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성이 지정된 구성 요소 클래스의 공용 속성을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="45a31-151">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="45a31-152">구성 요소의 `@code` C# 코드를 다음과 같이 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="45a31-153">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성이 지정된 공용 `IncrementAmount` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="45a31-154">`currentCount` 값을 증가시킬 때 `IncrementAmount` 속성을 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="45a31-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="45a31-156">특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount` 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="45a31-157">카운터가 10씩 증분하도록 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="45a31-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="45a31-159">`Index` 구성 요소를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-159">Reload the `Index` component.</span></span> <span data-ttu-id="45a31-160">단추가 선택될 때마다 카운터가 10씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="45a31-161">`Counter` 구성 요소의 카운터는 계속 1씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="45a31-162">구성 요소 경로</span><span class="sxs-lookup"><span data-stu-id="45a31-162">Route to components</span></span>

<span data-ttu-id="45a31-163">`Counter.razor` 파일 맨 위의 `@page` 지시문은 `Counter` 구성 요소가 라우팅 엔드포인트임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="45a31-164">`Counter` 구성 요소는 `/counter`로 전송된 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="45a31-165">`@page` 지시문이 없으면 구성 요소는 라우팅된 요청을 처리하지 않지만, 해당 구성 요소는 여전히 다른 구성 요소에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="45a31-166">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="45a31-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="45a31-167"> 서버 환경</span><span class="sxs-lookup"><span data-stu-id="45a31-167"> Server experience</span></span>

<span data-ttu-id="45a31-168">Blazor 서버 앱을 사용할 경우 `WeatherForecastService` 서비스가 `Startup.ConfigureServices`에서 [싱글톤](xref:fundamentals/dependency-injection#service-lifetimes)으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="45a31-169">해당 서비스의 인스턴스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)을 통해서 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="45a31-170">[`@inject`](xref:mvc/views/razor#inject) 지시문은 `WeatherForecastService` 서비스의 인스턴스를 `FetchData` 구성 요소에 주입하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="45a31-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="45a31-172">`FetchData` 구성 요소는 주입된 서비스를 `ForecastService`로 사용하여 `WeatherForecast` 개체의 배열을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="45a31-173"> WebAssembly 환경</span><span class="sxs-lookup"><span data-stu-id="45a31-173"> WebAssembly experience</span></span>

<span data-ttu-id="45a31-174">Blazor WebAssembly 앱을 사용할 경우 `wwwroot/sample-data` 폴더의 `weather.json` 파일에서 일기 예보 데이터를 가져오기 위해서 <xref:System.Net.Http.HttpClient>가 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="45a31-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="45a31-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="45a31-176">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프는 각 예측 인스턴스를 날씨 데이터 테이블의 행으로 렌더링하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="45a31-177">할 일 목록 빌드</span><span class="sxs-lookup"><span data-stu-id="45a31-177">Build a todo list</span></span>

<span data-ttu-id="45a31-178">앱에 간단한 할 일 목록을 구현하는 새 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="45a31-179">`Pages` 폴더에서 새 `Todo` Razor 구성 요소를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="45a31-180">Visual Studio에서 `Pages` 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** >  **Razor 구성 요소**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="45a31-181">구성 요소의 파일 이름을 `Todo.razor`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="45a31-182">다른 개발 환경에서는 `Todo.razor`라는 `Pages` 폴더에 빈 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> Razor<span data-ttu-id="45a31-183"> 구성 요소 파일 이름에서 첫 문자는 대문자여야 하므로 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-183"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="45a31-184">구성 요소에 대한 초기 태그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="45a31-185">`Todo` 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="45a31-186">`NavMenu` 구성 요소(`Shared/NavMenu.razor`)는 앱의 레이아웃에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="45a31-187">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="45a31-188">`Shared/NavMenu.razor` 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 `Todo` 구성 요소에 대한 `<NavLink>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="45a31-189">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-189">Rebuild and run the app.</span></span> <span data-ttu-id="45a31-190">새 Todo 페이지를 방문하여 `Todo` 구성 요소에 대한 링크가 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="45a31-191">프로젝트 루트에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="45a31-192">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="45a31-193">`Todo` 구성 요소(`Pages/Todo.razor`)로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="45a31-194">`@code` 블록에 있는 할 일 항목에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="45a31-195">`Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="45a31-196">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="45a31-197">목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="45a31-198">순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="45a31-199">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-199">Rebuild and run the app.</span></span> <span data-ttu-id="45a31-200">단추에 이벤트 처리기가 연결되어 있지 않으므로 **`Add todo`** 단추를 선택해도 아무 일도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="45a31-201">`Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 이를 단추 선택에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="45a31-202">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="45a31-203">새 할 일 항목의 제목을 가져오기 위해 `@code` 블록의 상단에 `newTodo` 문자열 필드를 추가하고 `<input>` 요소에서 `bind` 특성을 사용하여 이를 텍스트 입력 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="45a31-204">`AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="45a31-205">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="45a31-206">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-206">Rebuild and run the app.</span></span> <span data-ttu-id="45a31-207">할 일 목록에 몇 개의 할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="45a31-208">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="45a31-209">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="45a31-210">`@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="45a31-211">해당 값이 바인딩되었는지 확인하기 위해 `<h3>` 헤더를 수정하여 완료되지 않은(`IsDone`이 `false`) 할 일 항목 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="45a31-212">완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="45a31-213">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-213">Rebuild and run the app.</span></span> <span data-ttu-id="45a31-214">할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="45a31-215">다음 단계</span><span class="sxs-lookup"><span data-stu-id="45a31-215">Next steps</span></span>

<span data-ttu-id="45a31-216">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45a31-217">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="45a31-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="45a31-218">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="45a31-218">Modify Razor components</span></span>
> * <span data-ttu-id="45a31-219">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="45a31-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="45a31-220">Blazor 앱에서 DI(종속성 주입) 및 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="45a31-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="45a31-221">구성 요소를 빌드하고 사용하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="45a31-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
