---
title: ASP.NET 코어의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: ASP.NET 핵심 구성 요소 태그 도우미를 사용하여 페이지 및 보기에서 Razor 구성 요소를 렌더링하는 방법을 알아봅니다.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440963"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="79daa-103">ASP.NET 코어의 구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="79daa-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="79daa-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="79daa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="79daa-105">페이지 또는 뷰에서 구성요소를 렌더링하려면 [구성 요소 태그 도우미를](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)사용합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="79daa-106">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="79daa-106">Prerequisites</span></span>

<span data-ttu-id="79daa-107">문서의 페이지 및 보기 섹션에서 구성 요소를 사용할 앱 <xref:blazor/integrate-components#prepare-the-app> *준비의* 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="79daa-108">구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="79daa-108">Component Tag Helper</span></span>

<span data-ttu-id="79daa-109">다음 구성 요소 태그 도우미는 페이지 또는 보기에서 `Counter` 구성 요소를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="79daa-110">앞의 예제에서는 `Counter` 구성 요소가 앱의 페이지 폴더에 있다고 *가정합니다.*</span><span class="sxs-lookup"><span data-stu-id="79daa-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="79daa-111">구성 요소 태그 도우미는 매개 변수를 구성 요소에 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="79daa-112">확인란 `ColorfulCheckbox` 레이블의 색상과 크기를 설정하는 다음 구성 요소를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="79daa-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="79daa-113">`Size` (`int`) `Color` 및`string`() [구성 요소 매개 변수는](xref:blazor/components#component-parameters) 구성 요소 태그 도우미에 의해 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="79daa-114">앞의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="79daa-115">다음 HTML은 페이지 또는 보기에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="79daa-116">인용된 문자열을 전달하려면 앞의 예제와 같이 `param-Color` [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="79daa-117">형식 값에 대 한 `string` Razor 구문 분석 `param-*` 동작 특성 형식 `object` 이기 때문에 특성에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="79daa-118">매개 변수 형식은 JSON serializable이어야 하며, 일반적으로 형식에 기본 생성자 및 settable 속성이 있어야 함을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="79daa-119">`Size` 예를 들어 JSON `Color` serializer에서 지원하는 기본 형식(및)의 `Color` `int` `string` `Size` 형식과 형식이기 때문에 앞의 예제에서 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="79daa-120">다음 예제에서는 클래스 개체가 구성 요소에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="79daa-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="79daa-121">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="79daa-122">**클래스에는 공용 매개 변수 없는 생성자가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="79daa-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="79daa-123">*공유/마이컴포넌트.면도기*:</span><span class="sxs-lookup"><span data-stu-id="79daa-123">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="79daa-124">*페이지/마이페이지.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="79daa-124">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="79daa-125">앞의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="79daa-126">`MyClass`앱의 네임스페이스()에`{APP ASSEMBLY}`있습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="79daa-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>구성 요소:</span><span class="sxs-lookup"><span data-stu-id="79daa-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="79daa-128">페이지로 미리 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="79daa-129">페이지에서 정적 HTML로 렌더링되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보가 포함되어 있는 경우.</span><span class="sxs-lookup"><span data-stu-id="79daa-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="79daa-130">렌더 모드</span><span class="sxs-lookup"><span data-stu-id="79daa-130">Render Mode</span></span> | <span data-ttu-id="79daa-131">설명</span><span class="sxs-lookup"><span data-stu-id="79daa-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="79daa-132">구성 요소를 정적 HTML로 렌더링하고 서버 Blazor 앱에 대한 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="79daa-133">사용자 에이전트가 시작되면 이 마커가 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="79daa-134">서버 앱에 대한 Blazor 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="79daa-135">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-135">Output from the component isn't included.</span></span> <span data-ttu-id="79daa-136">사용자 에이전트가 시작되면 이 마커가 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="79daa-137">구성 요소를 정적 HTML로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="79daa-138">페이지와 뷰는 구성 요소를 사용할 수 있지만 반대는 사실이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="79daa-139">구성 요소는 부분 뷰 및 섹션과 같은 보기 및 페이지별 피쳐를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="79daa-140">구성 요소의 부분 뷰에서 논리를 사용하려면 부분 뷰 논리를 구성 요소로 팩터링합니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="79daa-141">정적 HTML 페이지에서 서버 구성 요소를 렌더링하는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="79daa-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79daa-142">추가 자료</span><span class="sxs-lookup"><span data-stu-id="79daa-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
