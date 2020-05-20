---
title: ASP.NET Core Blazor weasembomperformance 성능 모범 사례
author: pranavkm
description: ASP.NET Core Blazor weasembmbomapps의 성능을 향상 하 고 일반적인 성능 문제를 방지 하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 9e9b166cb9ce9870a8ff275b72bb12f04b84751b
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2020
ms.locfileid: "83439424"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="6073f-103">ASP.NET Core Blazor weasembomperformance 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="6073f-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="6073f-104">사람, [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="6073f-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="6073f-105">이 문서에서는 ASP.NET Core에 대 한 지침을 제공 Blazor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="6073f-106">불필요 한 구성 요소 렌더링 방지</span><span class="sxs-lookup"><span data-stu-id="6073f-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="6073f-107">알고리즘에서 구성 요소가 변경 되지 않았다는 것을 인식 하는 경우의 diff 알고리즘은 rerendering를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="6073f-108">구성 요소 렌더링을 세밀 하 게 제어 하려면 [Componentbase를 재정의 합니다.](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A)</span><span class="sxs-lookup"><span data-stu-id="6073f-108">Override [ComponentBase.ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) for fine-grained control over component rendering.</span></span>

<span data-ttu-id="6073f-109">초기 렌더링 이후 변경 되지 않는 UI 전용 구성 요소를 작성 하는 경우를 `ShouldRender` 반환 하도록를 구성 합니다 `false` .</span><span class="sxs-lookup"><span data-stu-id="6073f-109">If authoring a UI-only component that never changes after the initial render, configure `ShouldRender` to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="6073f-110">대부분의 앱은 세부적인 제어를 요구 하지 않지만 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> UI 이벤트에 응답 하는 구성 요소를 선택적으로 렌더링 하는 데 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="6073f-111">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="6073f-111">In the following example:</span></span>

* <span data-ttu-id="6073f-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>는를 재정의 하 고 필드의 값으로 설정 합니다 .이 필드의 값은 `shouldRender` 처음에 `false` 구성 요소를 로드할 때입니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the `shouldRender` field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="6073f-113">이 단추를 선택 하면 `shouldRender` 가로 설정 되어 `true` 구성 요소가 업데이트 된와 강제로 rerender 됩니다 `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="6073f-113">When the button is selected, `shouldRender` is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="6073f-114">Rerendering 바로 뒤 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 의 값을로 설정 하 여 `shouldRender` `false` 다음 번에 단추를 선택할 때까지 추가 rerendering 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of `shouldRender` back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="6073f-115">자세한 내용은 <xref:blazor/lifecycle#after-component-render>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6073f-115">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="6073f-116">다시 사용 가능한 조각 가상화</span><span class="sxs-lookup"><span data-stu-id="6073f-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="6073f-117">구성 요소는 코드 및 태그의 재사용 가능한 조각을 생성 하는 편리한 방법을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="6073f-118">일반적으로 앱의 요구 사항에 가장 잘 맞는 개별 구성 요소를 작성 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="6073f-119">한 가지 주의할 점은 각 추가 자식 구성 요소가 부모 구성 요소를 렌더링 하는 데 소요 되는 총 시간에 기여 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="6073f-120">대부분의 앱에서 추가 오버 헤드는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="6073f-121">많은 수의 구성 요소를 생성 하는 앱은 렌더링 된 구성 요소의 수를 제한 하는 것과 같은 처리 오버 헤드를 줄이기 위해 전략 사용을 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="6073f-122">예를 들어 구성 요소를 포함 하는 수백 개의 행을 렌더링 하는 표 또는 목록은 프로세서를 많이 사용 하 여 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="6073f-123">지정 된 시간에 구성 요소의 하위 집합만 렌더링 되도록 그리드 또는 목록 레이아웃을 가상화 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="6073f-124">구성 요소 하위 집합 렌더링의 예제는 [가상화 샘플 앱 (aspnet/Samples GitHub 리포지토리)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)에서 다음 구성 요소를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6073f-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="6073f-125">`Virtualize`구성 요소 ([공유/가상화. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): <xref:Microsoft.AspNetCore.Components.ComponentBase> 사용자 스크롤을 기준으로 날씨 데이터 행 집합을 렌더링 하기 위해를 구현 하는 c #으로 작성 된 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="6073f-126">`FetchData`구성 요소 ([Pages/fetchdata. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): 구성 요소를 사용 하 여 `Virtualize` 한 번에 25 개의 기상 데이터 행을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="6073f-127">JavaScript interop를 사용 하 여 데이터 마샬링 방지</span><span class="sxs-lookup"><span data-stu-id="6073f-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="6073f-128">BlazorWeasembmbomommbommbomomomomomomomomomomoma를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="6073f-129">두 컨텍스트 간에 콘텐츠를 serialize 하 고 deserialize 하면 앱에 대 한 처리 오버 헤드가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="6073f-130">JS interop 호출이 자주 발생 하면 성능이 저하 되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="6073f-131">경계를 넘어 데이터의 마샬링을 줄이기 위해 앱이 여러 개의 작은 페이로드를 단일 대규모 페이로드로 통합 하 여 Weasembomommbmboma와 JS 간 컨텍스트 전환의 양을 피할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="6073f-132">System.object 사용</span><span class="sxs-lookup"><span data-stu-id="6073f-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="6073f-133">의 JS interop 구현은 <xref:System.Text.Json> 메모리 할당이 낮은 고성능 JSON 직렬화 라이브러리인를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="6073f-134">를 사용 하면 <xref:System.Text.Json> 대체 JSON 라이브러리를 하나 이상 추가 하는 동안 추가 앱 페이로드 크기가 발생 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="6073f-135">마이그레이션 지침은 [newtonsoft.json에서 system.object로 마이그레이션하는 방법](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6073f-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="6073f-136">적절 한 경우 동기 및 마샬링됩니다 JS interop Api 사용</span><span class="sxs-lookup"><span data-stu-id="6073f-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="6073f-137">Weasembmboma <xref:Microsoft.JSInterop.IJSRuntime> 는 서버 앱에서 사용할 수 있는 단일 버전에 대 한 두 가지 추가 버전을 제공 합니다 Blazor .</span><span class="sxs-lookup"><span data-stu-id="6073f-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="6073f-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>비동기 버전 보다 오버 헤드가 적게 발생 하는 JS interop 호출을 동기식으로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="6073f-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>마샬링됩니다 JS interop 호출을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="6073f-140">를 사용 하는 경우 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> JS interop 방법의 오버 헤드가 가장 적게 발생 하는 반면, 이러한 api와 상호 작용 하는 데 필요한 JavaScript api는 현재 문서화 되지 않으며 이후 릴리스에서 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="6073f-141">앱 크기 줄이기</span><span class="sxs-lookup"><span data-stu-id="6073f-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="6073f-142">IL (중간 언어) 연결</span><span class="sxs-lookup"><span data-stu-id="6073f-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="6073f-143">[연결 Blazor Weasembomapps](xref:host-and-deploy/blazor/configure-linker) 는 앱의 이진에서 사용 하지 않는 코드를 잘라내어 앱 크기를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-143">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="6073f-144">기본적으로 링커는 구성에서 빌드하는 경우에만 사용할 수 `Release` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="6073f-145">이를 활용 하려면 [-c |--구성](/dotnet/core/tools/dotnet-publish#options) 옵션이로 설정 된 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용 하 여 배포용 앱을 게시 합니다 `Release` .</span><span class="sxs-lookup"><span data-stu-id="6073f-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="6073f-146">사용 하지 않는 기능 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="6073f-146">Disable unused features</span></span>

Blazor<span data-ttu-id="6073f-147">Weasemboms runtime에는 앱에서 더 작은 페이로드 크기를 요구 하지 않는 경우 사용 하지 않도록 설정할 수 있는 다음과 같은 .NET 기능이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-147"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="6073f-148">표준 시간대 정보를 정확 하 게 만들기 위한 데이터 파일이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-148">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="6073f-149">앱에이 기능이 필요 하지 않은 경우 `BlazorEnableTimeZoneSupport` 앱의 프로젝트 파일에서 MSBuild 속성을로 설정 하 여 사용 하지 않도록 설정 하는 것이 좋습니다 `false` .</span><span class="sxs-lookup"><span data-stu-id="6073f-149">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="6073f-150">데이터 정렬 정보는와 같은 Api를 제대로 작동 하도록 하는 데 포함 되어 <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6073f-150">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="6073f-151">응용 프로그램에 데이터 정렬 데이터가 필요 하지 않은 경우 `BlazorWebAssemblyPreserveCollationData` 응용 프로그램의 프로젝트 파일에서 MSBuild 속성을로 설정 하 여 사용 하지 않도록 설정 하는 것이 좋습니다 `false` .</span><span class="sxs-lookup"><span data-stu-id="6073f-151">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
