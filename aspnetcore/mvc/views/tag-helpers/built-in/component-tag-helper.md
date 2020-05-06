---
title: ASP.NET Core의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: ASP.NET Core 구성 요소 태그 도우미를 사용 하 여 페이지 및 Razor 뷰에서 구성 요소를 렌더링 하는 방법을 알아봅니다.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773931"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="e2ae5-103">ASP.NET Core의 구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="e2ae5-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e2ae5-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e2ae5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e2ae5-105">페이지 또는 뷰에서 구성 요소를 렌더링하려면 [구성 요소 태그 도우미](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2ae5-106">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e2ae5-106">Prerequisites</span></span>

<span data-ttu-id="e2ae5-107"><xref:blazor/integrate-components#prepare-the-app> 문서의 *페이지 및 뷰에서 구성 요소를 사용 하도록 앱 준비* 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="e2ae5-108">구성 요소 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="e2ae5-108">Component Tag Helper</span></span>

<span data-ttu-id="e2ae5-109">다음 구성 요소 태그 도우미는 페이지 `Counter` 또는 뷰에서 구성 요소를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="e2ae5-110">위의 예제에서는 `Counter` 구성 요소가 앱의 *Pages* 폴더에 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="e2ae5-111">구성 요소 태그 도우미는 구성 요소에 매개 변수를 전달할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="e2ae5-112">확인란 레이블의 색 `ColorfulCheckbox` 및 크기를 설정 하는 다음 구성 요소를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="e2ae5-113">( `Size` `int`) 및 `Color` (`string`) [구성 요소 매개 변수](xref:blazor/components#component-parameters) 는 구성 요소 태그 도우미를 통해 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="e2ae5-114">위의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="e2ae5-115">페이지 또는 뷰에서 렌더링 되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="e2ae5-116">앞의 예제 `param-Color` 에서와 같이 따옴표 붙은 문자열을 전달 하려면 [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="e2ae5-117">`string` 형식 값에 대 한 Razor 구문 분석 동작은 특성이 `object` 형식 이므로 `param-*` 특성에 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="e2ae5-118">매개 변수 형식은 JSON serializable 이어야 합니다 .이는 일반적으로 형식에 기본 생성자와 설정 가능한 속성이 있어야 함을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="e2ae5-119">`Size` 예를 들어 및 `Color` `Size` `Color` 의 형식이 JSON serializer에서 지원 되는 기본 형식 (`int` 및 `string`) 이기 때문에 앞의 예제에서 및에 대 한 값을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="e2ae5-120">다음 예제에서는 클래스 개체가 구성 요소에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="e2ae5-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2ae5-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="e2ae5-122">**클래스에는 매개 변수가 없는 public 생성자가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="e2ae5-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="e2ae5-123">*Shared/MyComponent*:</span><span class="sxs-lookup"><span data-stu-id="e2ae5-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="e2ae5-124">*Pages/m*:</span><span class="sxs-lookup"><span data-stu-id="e2ae5-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="e2ae5-125">위의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있는 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="e2ae5-126">`MyClass`는 응용 프로그램의 네임 스페이스 (`{APP ASSEMBLY}`)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="e2ae5-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e2ae5-128">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="e2ae5-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="e2ae5-129">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="e2ae5-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="e2ae5-130">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="e2ae5-130">Render Mode</span></span> | <span data-ttu-id="e2ae5-131">설명</span><span class="sxs-lookup"><span data-stu-id="e2ae5-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e2ae5-132">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2ae5-133">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e2ae5-134">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e2ae5-135">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-135">Output from the component isn't included.</span></span> <span data-ttu-id="e2ae5-136">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e2ae5-137">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e2ae5-138">페이지 및 뷰에서 구성 요소를 사용할 수 있지만 반대의 경우는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="e2ae5-139">구성 요소는 부분 보기 및 섹션과 같은 보기 및 페이지 관련 기능을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="e2ae5-140">구성 요소의 부분 뷰에서 논리를 사용 하려면 부분 뷰 논리를 구성 요소로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="e2ae5-141">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2ae5-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2ae5-142">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2ae5-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
