---
title: ASP.NET Core Blazor 수명 주기
author: guardrex
description: ASP.NET Core Blazor 앱에서 Razor 구성 요소 수명 주기 메서드를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438917"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="df021-103">ASP.NET Core Blazor 수명 주기</span><span class="sxs-lookup"><span data-stu-id="df021-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="df021-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df021-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="df021-105">Blazor 프레임워크는 동기 및 비동기 수명 주기 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="df021-106">구성 요소 초기화 및 렌더링 중에 구성 요소에서 추가 작업을 수행하려면 수명 주기 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="df021-107">수명 주기 메서드</span><span class="sxs-lookup"><span data-stu-id="df021-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="df021-108">구성 요소 초기화 메서드</span><span class="sxs-lookup"><span data-stu-id="df021-108">Component initialization methods</span></span>

<span data-ttu-id="df021-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>는 구성 요소가 부모 구성 요소에서 초기 매개 변수를 받은 후 초기화할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="df021-110">구성 요소가 비동기 작업을 수행하며, 작업이 완료될 때 새로 고쳐야 하는 경우 `OnInitializedAsync`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="df021-111">동기 작업의 경우 `OnInitialized`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="df021-112">비동기 작업을 수행하려면 `OnInitializedAsync`를 재정의하고, 작업에 `await` 키워드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="df021-113">[콘텐츠를 미리 렌더링](xref:blazor/hosting-model-configuration#render-mode)하는 Blazor 서버 앱은 `OnInitializedAsync`를 **두 번** 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="df021-114">첫 번째 호출: 구성 요소가 처음에 페이지 일부로 정적 렌더링될 때</span><span class="sxs-lookup"><span data-stu-id="df021-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="df021-115">두 번째 호출: 브라우저가 서버에 다시 연결할 때</span><span class="sxs-lookup"><span data-stu-id="df021-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="df021-116">`OnInitializedAsync`의 개발자 코드가 두 번 실행되는 것을 방지하려면 [미리 렌더링한 후의 상태 저장 다시 연결](#stateful-reconnection-after-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="df021-117">Blazor 서버 앱을 미리 렌더링 중이면 브라우저에 연결되어 있지 않으므로 JavaScript 호출 등의 특정 작업을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="df021-118">미리 렌더링된 경우, 구성 요소를 다르게 렌더링해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="df021-119">자세한 내용은 [앱을 미리 렌더링 중인 경우 검색](#detect-when-the-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="df021-120">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df021-121">자세한 내용은 [IDisposable을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 단원을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="df021-122">매개 변수를 설정하기 전</span><span class="sxs-lookup"><span data-stu-id="df021-122">Before parameters are set</span></span>

<span data-ttu-id="df021-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>는 렌더링 트리에서 구성 요소의 부모가 제공하는 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="df021-124">`SetParametersAsync`를 호출할 때마다 <xref:Microsoft.AspNetCore.Components.ParameterView>에 매개 변수 값의 전체 집합이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="df021-125">`SetParametersAsync`의 기본 구현에서는 `ParameterView`에 해당 값이 있는 `[Parameter]` 또는 `[CascadingParameter]` 특성을 사용하여 각 속성의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="df021-126">`ParameterView`에 해당 값이 없는 매개 변수는 변경되지 않고 그대로 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="df021-127">`base.SetParametersAync`를 호출하지 않은 경우, 사용자 지정 코드는 들어오는 매개 변수 값을 필요한 방식으로 해석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="df021-128">예를 들어 들어오는 매개 변수를 클래스의 속성에 할당해야 하는 요구 사항이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="df021-129">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df021-130">자세한 내용은 [IDisposable을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 단원을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="df021-131">매개 변수를 설정한 후</span><span class="sxs-lookup"><span data-stu-id="df021-131">After parameters are set</span></span>

<span data-ttu-id="df021-132">다음 두 가지 경우에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="df021-133">구성 요소가 초기화되고 부모 구성 요소에서 첫 번째 매개 변수 집합을 받은 경우</span><span class="sxs-lookup"><span data-stu-id="df021-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="df021-134">부모 구성 요소가 다시 렌더링되고 다음을 제공하는 경우</span><span class="sxs-lookup"><span data-stu-id="df021-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="df021-135">하나 이상의 매개 변수가 변경된, 알려진 변경 불가능 기본 형식만</span><span class="sxs-lookup"><span data-stu-id="df021-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="df021-136">복합 형식 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="df021-136">Any complex-typed parameters.</span></span> <span data-ttu-id="df021-137">프레임워크는 복합 형식 매개 변수의 값이 내부적으로 변경되었는지 아닌지를 알 수 없으므로 매개 변수 집합을 변경된 것으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="df021-138">매개 변수와 속성 값을 적용하는 경우 비동기 작업은 `OnParametersSetAsync` 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="df021-139">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df021-140">자세한 내용은 [IDisposable을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 단원을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="df021-141">구성 요소 렌더링 후</span><span class="sxs-lookup"><span data-stu-id="df021-141">After component render</span></span>

<span data-ttu-id="df021-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 및 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>는 구성 요소 렌더링을 완료한 후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="df021-143">이때 요소 및 구성 요소 참조가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="df021-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="df021-144">렌더링된 DOM 요소에서 작동하는 타사 JavaScript 라이브러리 활성화와 같은 추가 초기화 단계를 렌더링된 콘텐츠로 수행하려면 이 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="df021-145">`OnAfterRenderAsync` 및 `OnAfterRender`의 `firstRender` 매개 변수와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="df021-146">구성 요소 인스턴스를 처음 렌더링할 때는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="df021-147">초기화 작업이 한 번만 수행되도록 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="df021-148">렌더링 직후의 비동기 작업은 `OnAfterRenderAsync` 수명 주기 이벤트 중에 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="df021-149">`OnAfterRenderAsync`에서 <xref:System.Threading.Tasks.Task>를 반환하는 경우에도, 해당 작업이 완료된 후에는 프레임워크에서 구성 요소에 대해 추가 렌더링 주기를 예약하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="df021-150">이 동작은 무한 렌더링 루프를 방지하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="df021-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="df021-151">반환된 작업이 완료된 후 추가 렌더링 주기를 예약하는 기타 수명 주기 메서드와는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="df021-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="df021-152">`OnAfterRender` 및 `OnAfterRenderAsync`‘는 서버에서 미리 렌더링되는 경우 호출되지 않습니다.’</span><span class="sxs-lookup"><span data-stu-id="df021-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="df021-153">이벤트 처리기가 설정된 경우 삭제 시 해당 처리기를 언후크합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="df021-154">자세한 내용은 [IDisposable을 사용한 구성 요소 삭제](#component-disposal-with-idisposable) 단원을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="df021-155">UI 새로 고침 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="df021-155">Suppress UI refreshing</span></span>

<span data-ttu-id="df021-156">UI 새로 고침을 표시하지 않으려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="df021-157">구현에서 `true`를 반환하는 경우 UI가 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="df021-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="df021-158">구성 요소를 렌더링할 때마다 `ShouldRender`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="df021-159">`ShouldRender`를 재정의한 경우에도 처음에는 구성 요소가 항상 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="df021-160">자세한 내용은 <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="df021-161">상태 변경</span><span class="sxs-lookup"><span data-stu-id="df021-161">State changes</span></span>

<span data-ttu-id="df021-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>는 구성 요소에 상태가 변경되었음을 알립니다.</span><span class="sxs-lookup"><span data-stu-id="df021-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="df021-163">해당하는 경우, `StateHasChanged`를 호출하면 구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="df021-164">렌더링 시 불완전한 비동기 작업 처리</span><span class="sxs-lookup"><span data-stu-id="df021-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="df021-165">수명 주기 이벤트에서 수행한 비동기 작업이 구성 요소를 렌더링하기 전에 완료되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="df021-166">수명 주기 메서드를 실행하는 동안 개체가 `null`이거나 불완전하게 데이터로 채워졌을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="df021-167">개체가 초기화되었는지 확인하는 렌더링 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="df021-168">개체가 `null`인 동안 자리 표시자 UI 요소(예: 로드 메시지)를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="df021-169">Blazor 템플릿의 `FetchData` 구성 요소에서 `OnInitializedAsync`는 예측 데이터(`forecasts`)를 비동기적으로 수신하도록 재정의되었습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="df021-170">`forecasts`가 `null`인 경우 사용자에게 로드 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="df021-171">`OnInitializedAsync`에서 반환된 `Task`가 완료되면 구성 요소가 업데이트된 상태로 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="df021-172">Blazor 서버 템플릿의 *Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="df021-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="df021-173">IDisposable을 사용한 구성 요소 삭제</span><span class="sxs-lookup"><span data-stu-id="df021-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="df021-174">구성 요소가 <xref:System.IDisposable>을 구현하는 경우, UI에서 구성 요소를 제거할 때 [Dispose 메서드](/dotnet/standard/garbage-collection/implementing-dispose)가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="df021-175">다음 구성 요소는 `@implements IDisposable` 및 `Dispose` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="df021-176">`Dispose`에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 호출은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="df021-177">렌더러를 삭제하는 과정에서 `StateHasChanged`가 호출될 수 있으므로, 해당 시점에 UI 업데이트를 요청할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="df021-178">.NET 이벤트에서 이벤트 처리기의 구독을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="df021-179">다음 [Blazor 양식](xref:blazor/forms-validation) 예제에서는 `Dispose` 메서드에서 이벤트 처리기를 언후크하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df021-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="df021-180">프라이빗 필드 및 람다 접근 방식</span><span class="sxs-lookup"><span data-stu-id="df021-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="df021-181">프라이빗 메서드 접근 방식</span><span class="sxs-lookup"><span data-stu-id="df021-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="df021-182">오류 처리</span><span class="sxs-lookup"><span data-stu-id="df021-182">Handle errors</span></span>

<span data-ttu-id="df021-183">수명 주기 메서드 실행 중의 오류 처리 방법에 대한 자세한 내용은 <xref:blazor/handle-errors#lifecycle-methods>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="df021-184">미리 렌더링 후의 상태 저장 다시 연결</span><span class="sxs-lookup"><span data-stu-id="df021-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="df021-185">Blazor 서버 앱에서 `RenderMode`가 `ServerPrerendered`인 경우, 구성 요소는 처음에 페이지 일부로 정적 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="df021-186">브라우저가 서버에 다시 연결하면 구성 요소가 ‘다시’ 렌더링되고, 이제 대화형 구성 요소가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="df021-187">구성 요소를 초기화하기 위한 [OnInitialized{Async}](#component-initialization-methods) 수명 주기 메서드가 있는 경우 메서드가 다음과 같이 ‘두 번’ 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="df021-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="df021-188">구성 요소를 정적으로 미리 렌더링할 때</span><span class="sxs-lookup"><span data-stu-id="df021-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="df021-189">서버 연결이 설정된 후</span><span class="sxs-lookup"><span data-stu-id="df021-189">After the server connection has been established.</span></span>

<span data-ttu-id="df021-190">이 동작 때문에 구성 요소를 최종적으로 렌더링할 때는 UI에 표시되는 데이터가 상당히 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="df021-191">Blazor 서버 앱에서 이중 렌더링 시나리오를 방지하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="df021-192">미리 렌더링 중에 상태를 캐시하고 앱이 다시 시작된 후 상태를 검색하는 데 사용할 수 있는 식별자를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="df021-193">미리 렌더링 중에 식별자를 사용하여 구성 요소 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="df021-194">미리 렌더링 후에 식별자를 사용하여 캐시된 상태를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="df021-195">다음 코드는 템플릿 기반 Blazor 서버 앱에서 이중 렌더링을 방지하도록 업데이트된 `WeatherForecastService`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="df021-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="df021-196">`RenderMode`에 대한 자세한 내용은 <xref:blazor/hosting-model-configuration#render-mode>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="df021-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="df021-197">앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="df021-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="df021-198">취소할 수 있는 백그라운드 작업</span><span class="sxs-lookup"><span data-stu-id="df021-198">Cancelable background work</span></span>

<span data-ttu-id="df021-199">구성 요소는 네트워크 호출(<xref:System.Net.Http.HttpClient>) 및 데이터베이스와의 상호 작용 등과 같은 장기 실행 백그라운드 작업을 수행하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="df021-200">여러 상황에서 시스템 리소스를 절약하기 위해 백그라운드 작업을 중지하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="df021-201">예를 들어 사용자가 구성 요소에서 이동할 때 백그라운드 비동기 작업이 자동으로 중지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="df021-202">백그라운드 작업 항목에 취소가 필요할 수 있는 다른 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="df021-203">잘못된 입력 데이터 또는 처리 매개 변수를 사용하여 실행 중인 백그라운드 작업이 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="df021-204">현재 실행 중인 백그라운드 작업 항목 집합을 새 작업 항목 집합으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="df021-205">현재 실행 중인 작업의 우선 순위를 변경해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="df021-206">서버에 다시 배포하기 위해 앱을 종료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="df021-207">서버 리소스가 제한되므로 백그라운드 작업 항목을 다시 예약해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="df021-208">구성 요소에서 취소할 수 있는 백그라운드 작업 패턴을 구현하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="df021-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="df021-209"><xref:System.Threading.CancellationTokenSource> 및 <xref:System.Threading.CancellationToken>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="df021-210">[구성 요소를 삭제](#component-disposal-with-idisposable)할 때 그리고 토큰을 수동으로 취소할 필요가 있을 때, [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A)을 호출하여 백그라운드 작업을 취소해야 한다는 신호를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="df021-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="df021-211">비동기 호출이 반환된 후에는 토큰에서 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="df021-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="df021-212">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="df021-212">In the following example:</span></span>

* <span data-ttu-id="df021-213">`await Task.Delay(5000, cts.Token);`는 장기 실행 비동기 백그라운드 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="df021-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="df021-214">`BackgroundResourceMethod`는 호출되기 전에 `Resource`가 삭제된 경우 시작하지 않아야 하는 장기 실행 백그라운드 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="df021-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
