---
title: ASP.NET 코어의 구성 요소 태그 도우미
author: guardrex
ms.author: riande
description: ASP.NET 핵심 구성 요소 태그 도우미를 사용하여 페이지 및 보기에서 Razor 구성 요소를 렌더링하는 방법을 알아봅니다.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123423"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET 코어의 구성 요소 태그 도우미

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

페이지 또는 뷰에서 구성요소를 렌더링하려면 [구성 요소 태그 도우미를](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

문서의 페이지 및 보기 섹션에서 구성 요소를 사용할 앱 <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> *준비의* 지침을 따릅니다.

## <a name="component-tag-helper"></a>구성 요소 태그 도우미

다음 구성 요소 태그 도우미는 페이지 또는 보기에서 `Counter` 구성 요소를 렌더링합니다.

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

앞의 예제에서는 `Counter` 구성 요소가 앱의 페이지 폴더에 있다고 *가정합니다.*

구성 요소 태그 도우미는 매개 변수를 구성 요소에 전달할 수도 있습니다. 확인란 `ColorfulCheckbox` 레이블의 색상과 크기를 설정하는 다음 구성 요소를 고려하십시오.

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

`Size` (`int`) `Color` 및`string`() [구성 요소 매개 변수는](xref:blazor/components#component-parameters) 구성 요소 태그 도우미에 의해 설정할 수 있습니다.

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

앞의 예제에서는 `ColorfulCheckbox` 구성 요소가 앱의 *공유* 폴더에 있다고 가정합니다.

다음 HTML은 페이지 또는 보기에서 렌더링됩니다.

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

인용된 문자열을 전달하려면 앞의 예제와 같이 `param-Color` [명시적 Razor 식이](xref:mvc/views/razor#explicit-razor-expressions)필요합니다. 형식 값에 대 한 `string` Razor 구문 분석 `param-*` 동작 특성 형식 `object` 이기 때문에 특성에 적용 되지 않습니다.

매개 변수 형식은 JSON serializable이어야 하며, 일반적으로 형식에 기본 생성자 및 settable 속성이 있어야 함을 의미합니다. `Size` 예를 들어 JSON `Color` serializer에서 지원하는 기본 형식(및)의 `Color` `int` `string` `Size` 형식과 형식이기 때문에 앞의 예제에서 값을 지정할 수 있습니다.

다음 예제에서는 클래스 개체가 구성 요소에 전달됩니다.

*MyClass.cs*:

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

**클래스에는 공용 매개 변수 없는 생성자가 있어야 합니다.**

*공유/마이컴포넌트.면도기*:

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

*페이지/마이페이지.cshtml*:

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

앞의 예제에서는 `MyComponent` 구성 요소가 앱의 *공유* 폴더에 있다고 가정합니다. `MyClass`앱의 네임스페이스()에`{APP ASSEMBLY}`있습니다.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>구성 요소:

* 페이지로 미리 렌더링됩니다.
* 페이지에서 정적 HTML로 렌더링되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보가 포함되어 있는 경우.

| 렌더 모드 | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 구성 요소를 정적 HTML로 렌더링하고 서버 Blazor 앱에 대한 마커를 포함합니다. 사용자 에이전트가 시작되면 이 마커가 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 서버 앱에 대한 Blazor 마커를 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트가 시작되면 이 마커가 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 구성 요소를 정적 HTML로 렌더링합니다. |

페이지와 뷰는 구성 요소를 사용할 수 있지만 반대는 사실이 아닙니다. 구성 요소는 부분 뷰 및 섹션과 같은 보기 및 페이지별 피쳐를 사용할 수 없습니다. 구성 요소의 부분 뷰에서 논리를 사용하려면 부분 뷰 논리를 구성 요소로 팩터링합니다.

정적 HTML 페이지에서 서버 구성 요소를 렌더링하는 것은 지원되지 않습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
