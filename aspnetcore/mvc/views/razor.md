---
title: ASP.NET Core에 대 한 razor 구문 참조
author: rick-anderson
description: Razor웹 페이지에 서버 기반 코드를 포함 하는 태그 구문에 대해 알아봅니다.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424416"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="6c628-103">ASP.NET Core에 대한 Razor 구문 참조</span><span class="sxs-lookup"><span data-stu-id="6c628-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="6c628-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Mullen](https://twitter.com/ntaylormullen)및 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="6c628-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="6c628-105">Razor는 웹 페이지에 서버 기반 코드를 포함하는 태그 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="6c628-106">Razor 구문은 Razor 태그, C# 및 HTML로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="6c628-107">Razor를 포함하는 파일의 확장명은 일반적으로 *.cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="6c628-108">Razor는 [Razor 구성 요소](xref:blazor/components) 파일(*.razor*)에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="6c628-109">HTML 렌더링</span><span class="sxs-lookup"><span data-stu-id="6c628-109">Rendering HTML</span></span>

<span data-ttu-id="6c628-110">기본 Razor 언어는 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-110">The default Razor language is HTML.</span></span> <span data-ttu-id="6c628-111">Razor 태그에서 HTML을 렌더링하는 방법은 HTML 파일에서 HTML을 렌더링하는 방법과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="6c628-112">*.cshtml* Razor 파일의 HTML 태그는 변경되지 않은 서버에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="6c628-113">Razor 구문</span><span class="sxs-lookup"><span data-stu-id="6c628-113">Razor syntax</span></span>

<span data-ttu-id="6c628-114">Razor는 C#을 지원하며 `@` 기호를 사용하여 HTML에서 C#으로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="6c628-115">Razor는 C# 식을 평가하여 HTML 출력에서 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="6c628-116">`@` 기호 뒤에 [Razor 예약 키워드](#razor-reserved-keywords)가 사용되면 이 기호는 Razor 관련 태그로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="6c628-117">그렇지 않으면 C#으로 전환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="6c628-118">Razor 태그에서 `@` 기호를 이스케이프하려면 두 번째 `@` 기호를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="6c628-119">코드는 단일 `@` 기호를 사용하여 HTML로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="6c628-120">이메일 주소를 포함하는 HTML 특성 및 콘텐츠는 `@` 기호를 전환 문자로 취급하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="6c628-121">다음 예제의 이메일 주소는 Razor 구문 분석에 의해 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="6c628-122">암시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="6c628-122">Implicit Razor expressions</span></span>

<span data-ttu-id="6c628-123">암시적 Razor 식은 `@`으로 시작하고 그 뒤에 C# 코드가 옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="6c628-124">C# `await` 키워드를 제외하고, 암시적 식에 공백이 있으면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="6c628-125">C# 문에 명확한 끝이 있으면 공백을 혼합 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="6c628-126">암시적 식은 C# 제네릭을 포함할 수 **없습니다**. 대괄호(`<>`) 안에 있는 문자가 HTML 태그로 해석되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="6c628-127">다음 코드는 유효하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="6c628-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="6c628-128">위의 코드는 다음 중 하나와 비슷한 컴파일러 오류를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="6c628-129">"Int" 요소가 종료되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="6c628-130">모든 요소는 하나 있어야 자체적으로 닫히거나 일치하는 끝 태그가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="6c628-131">'GenericMethod' 메서드 그룹을 비대리자 형식 '개체'로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="6c628-132">메서드를 호출할 생각이었나요?</span><span class="sxs-lookup"><span data-stu-id="6c628-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="6c628-133">제네릭 메서드 호출은 [명시적 Razor 식](#explicit-razor-expressions) 또는 [Razor 코드 블록](#razor-code-blocks)에 래핑되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="6c628-134">명시적 Razor 식</span><span class="sxs-lookup"><span data-stu-id="6c628-134">Explicit Razor expressions</span></span>

<span data-ttu-id="6c628-135">명시적 Razor 식은 `@` 기호와 균형 잡힌 괄호로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="6c628-136">지난 주의 시간을 렌더링하기 위해 다음 Razor 태그가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="6c628-137">`@()` 괄호 안의 모든 콘텐츠가 평가되고 출력에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="6c628-138">이전 섹션에서 설명한 암시적 식은 일반적으로 공백을 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="6c628-139">다음 코드에서 일주일은 현재 시간에서 차감되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="6c628-140">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="6c628-141">명시적 식은 텍스트를 식 결과와 연결하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="6c628-142">명시적 식이 없으면 `<p>Age@joe.Age</p>`는 이메일 주소로 처리되고 `<p>Age@joe.Age</p>`가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="6c628-143">명시적 식으로 작성되면 `<p>Age33</p>`이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="6c628-144">명시적 식은 *.cshtml* 파일에 있는 제네릭 메서드의 출력을 렌더링하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="6c628-145">다음 표시는 앞에서 C# 제네릭의 대괄호로 인해 발생한 오류를 해결하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="6c628-146">이 코드는 명시적 식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="6c628-147">식 인코딩</span><span class="sxs-lookup"><span data-stu-id="6c628-147">Expression encoding</span></span>

<span data-ttu-id="6c628-148">문자열로 확인되는 C# 식은 인코딩된 HTML입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="6c628-149">`IHtmlContent`로 확인되는 C# 식은 `IHtmlContent.WriteTo`를 통해 직접 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="6c628-150">`IHtmlContent`로 확인되지 않는 C# 식은 `ToString`을 통해 문자열로 변환되고 인코딩된 후 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="6c628-151">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="6c628-152">다음과 같은 이유로 HTML은 브라우저에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="6c628-153">`HtmlHelper.Raw` 출력은 인코딩되지 않지만 HTML 태그로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="6c628-154">삭제되지 않은 사용자 입력에서 `HtmlHelper.Raw`를 사용하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="6c628-155">사용자 입력에 악의적인 JavaScript 또는 다른 악용 기법이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="6c628-156">사용자 입력을 제거하기는 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="6c628-157">사용자 입력에 `HtmlHelper.Raw`를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="6c628-158">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="6c628-159">Razor 코드 블록</span><span class="sxs-lookup"><span data-stu-id="6c628-159">Razor code blocks</span></span>

<span data-ttu-id="6c628-160">Razor 코드 블록은 `@`으로 시작하고 `{}`로 묶입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="6c628-161">식과는 달리, 코드 블록 내부의 C# 코드는 렌더링되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="6c628-162">보기의 코드 블록과 식은 같은 범위를 공유하고 순서대로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="6c628-163">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c628-164">코드 블록에서 템플릿 메서드로 제공되는 태그를 사용하여 [로컬 함수](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="6c628-165">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="6c628-166">암시적 전환</span><span class="sxs-lookup"><span data-stu-id="6c628-166">Implicit transitions</span></span>

<span data-ttu-id="6c628-167">코드 블록의 기본 언어는 C#이지만, Razor 페이지를 다시 HTML로 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="6c628-168">구분 기호로 분리된 명시적 전환</span><span class="sxs-lookup"><span data-stu-id="6c628-168">Explicit delimited transition</span></span>

<span data-ttu-id="6c628-169">HTML을 렌더링해야 하는 코드 블록의 하위 섹션을 정의하려면 렌더링할 문자를 Razor `<text>` 태그로 묶어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="6c628-170">HTML 태그로 묶이지 않은 HTML을 렌더링하려면 이 방법을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="6c628-171">HTML 또는 Razor 태그가 없으면 Razor 런타임 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="6c628-172">`<text>` 태그는 콘텐츠를 렌더링할 때 공백을 제어하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="6c628-173">`<text>` 태그 사이의 콘텐츠만 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="6c628-174">`<text>` 태그 앞 또는 뒤에 있는 공백은 HTML 출력에 나타나지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="6c628-175">명시적 줄 전환</span><span class="sxs-lookup"><span data-stu-id="6c628-175">Explicit line transition</span></span>

<span data-ttu-id="6c628-176">코드 블록 내부의 나머지 전체 줄을 HTML로 렌더링하려면 `@:` 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="6c628-177">코드에 `@:`이 없으면 Razor 런타임 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="6c628-178">Razor 파일의 추가 `@` 문자는 블록의 뒷부분에 나오는 명령문에서 컴파일러 오류를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="6c628-179">실제 오류가 보고된 오류보다 먼저 발생하기 때문에 이러한 컴파일러 오류를 이해하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="6c628-180">이 오류는 여러 암시적/명시적 식을 단일 코드 블록에 결합한 이후에 자주 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="6c628-181">제어 구조</span><span class="sxs-lookup"><span data-stu-id="6c628-181">Control structures</span></span>

<span data-ttu-id="6c628-182">제어 구조는 코드 블록의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="6c628-183">코드 블록의 모든 측면(태그로 전환, 인라인 C#)은 다음 구조에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="6c628-184">조건부 \@if, else if, else 및 \@switch</span><span class="sxs-lookup"><span data-stu-id="6c628-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="6c628-185">`@if`는 코드가 실행되는 시기를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="6c628-186">`else` 및 `else if`는 `@` 기호가 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="6c628-187">다음 태그는 switch 문 사용 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="6c628-188">반복 \@for, \@foreach, \@while 및 \@do while</span><span class="sxs-lookup"><span data-stu-id="6c628-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="6c628-189">템플릿 기반 HTML은 반복 제어 문으로 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="6c628-190">사람 목록을 렌더링하려면:</span><span class="sxs-lookup"><span data-stu-id="6c628-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="6c628-191">다음 반복 문이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="6c628-192">복합 \@using</span><span class="sxs-lookup"><span data-stu-id="6c628-192">Compound \@using</span></span>

<span data-ttu-id="6c628-193">C#에서 `using` 문은 개체가 삭제되도록 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="6c628-194">Razor에서는 동일한 메커니즘을 사용하여 추가 콘텐츠를 포함하는 HTML 도우미를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="6c628-195">다음 코드에서 HTML 도우미는 `@using` 문을 사용하여 `<form>` 태그를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="6c628-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="6c628-196">\@try, catch, finally</span></span>

<span data-ttu-id="6c628-197">예외 처리는 C#과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="6c628-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="6c628-198">\@lock</span></span>

<span data-ttu-id="6c628-199">Razor는 lock 문을 사용하여 중요한 섹션을 보호하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="6c628-200">주석</span><span class="sxs-lookup"><span data-stu-id="6c628-200">Comments</span></span>

<span data-ttu-id="6c628-201">Razor는 C# 및 HTML 주석을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="6c628-202">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="6c628-203">Razor 주석은 웹 페이지가 렌더링되기 전에 서버에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="6c628-204">Razor는 `@*  *@`을 사용하여 주석을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="6c628-205">다음 코드는 주석 처리되며, 따라서 서버에서 어떤 태그도 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="6c628-206">지시문</span><span class="sxs-lookup"><span data-stu-id="6c628-206">Directives</span></span>

<span data-ttu-id="6c628-207">Razor 지시문은 `@` 기호 뒤에 예약된 키워드를 사용하여 암시적 식으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="6c628-208">지시문은 일반적으로 보기를 구문 분석하는 방식을 변경하거나 다른 기능을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="6c628-209">Razor가 보기에 대한 코드를 생성하는 원리를 이해하면 지시문의 작동 원리를 쉽게 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="6c628-210">이 코드는 다음과 비슷한 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="6c628-211">이 문서의 뒷부분에 나오는 [보기용으로 생성된 Razor C# 클래스 검사](#inspect-the-razor-c-class-generated-for-a-view) 섹션에서는 생성된 이 클래스를 보는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="6c628-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="6c628-212">\@attribute</span></span>

<span data-ttu-id="6c628-213">`@attribute` 지시문은 지정된 특성을 생성된 페이지 또는 보기의 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="6c628-214">다음 예제에서는 `[Authorize]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="6c628-215">\@code</span><span class="sxs-lookup"><span data-stu-id="6c628-215">\@code</span></span>

<span data-ttu-id="6c628-216">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-217">`@code` 블록을 사용하면 [Razor 구성 요소](xref:blazor/components)를 통해 C# 멤버(필드, 속성 및 메서드)를 구성 요소에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="6c628-218">Razor 구성 요소의 경우 `@code` 는의 별칭이 [`@functions`](#functions) 며 권장 됩니다 `@functions` .</span><span class="sxs-lookup"><span data-stu-id="6c628-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="6c628-219">두 개 이상의 `@code` 블록이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="6c628-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="6c628-220">\@functions</span></span>

<span data-ttu-id="6c628-221">`@functions` 지시문을 사용하면 C# 멤버(필드, 속성 및 메서드)를 생성된 클래스에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c628-222">[Razor 구성 요소](xref:blazor/components)에서 `@functions` 대신 `@code`를 사용하여 C# 멤버를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="6c628-223">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="6c628-224">이 코드는 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="6c628-225">다음 코드는 생성된 Razor C# 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c628-226">`@functions` 메서드는 태그가 있을 때 템플릿 메서드로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="6c628-227">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="6c628-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="6c628-228">\@implements</span></span>

<span data-ttu-id="6c628-229">`@implements` 지시문은 생성된 클래스의 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="6c628-230">다음 예제에서는 <xref:System.IDisposable.Dispose*> 메서드를 호출할 수 있도록 <xref:System.IDisposable?displayProperty=fullName>을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="6c628-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="6c628-231">\@inherits</span></span>

<span data-ttu-id="6c628-232">`@inherits` 지시문은 보기에서 상속하는 클래스에 대한 완전한 제어권을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="6c628-233">다음 코드는 사용자 지정 Razor 페이지 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="6c628-234">`CustomText`는 보기에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="6c628-235">이 코드는 다음 HTML을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="6c628-236">`@model` 및 `@inherits`는 동일한 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="6c628-237">`@inherits`는 보기에서 가져오는 *_ViewImports.cshtml* 파일에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="6c628-238">다음 코드는 강력한 형식의 보기 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="6c628-239">"rick@contoso.com"이 모델에 전달되면 보기에서 다음과 같은 HTML 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="6c628-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="6c628-240">\@inject</span></span>

<span data-ttu-id="6c628-241">`@inject` 지시문을 사용하면 Razor 페이지에서 [서비스 컨테이너](xref:fundamentals/dependency-injection)의 서비스를 보기에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="6c628-242">자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="6c628-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="6c628-243">\@layout</span></span>

<span data-ttu-id="6c628-244">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-245">`@layout` 지시문에서는 Razor 구성 요소의 레이아웃을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="6c628-246">레이아웃 구성 요소는 코드 중복 및 불일치를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="6c628-247">자세한 내용은 <xref:blazor/layouts>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="6c628-248">\@model</span><span class="sxs-lookup"><span data-stu-id="6c628-248">\@model</span></span>

<span data-ttu-id="6c628-249">*이 시나리오는 MVC 보기와 Razor Pages(cshtml)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="6c628-250">`@model` 지시문은 보기 또는 페이지에 전달되는 모델 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="6c628-251">개별 사용자 계정을 사용하여 만든 ASP.NET Core MVC 또는 Razor Pages 앱에서, *Views/Account/Login.cshtml*에는 다음과 같은 모델 선언이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="6c628-252">생성된 클래스는 `RazorPage<dynamic>`에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="6c628-253">Razor는 보기에 전달된 모델에 액세스할 수 있는 `Model` 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="6c628-254">`@model` 지시문은 `Model` 속성의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="6c628-255">이 지시문은 보기가 파생되는 클래스를 생성한 `RazorPage<T>`의 `T`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="6c628-256">`@model` 지시문이 지정되지 않을 경우 `Model` 속성은 `dynamic` 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="6c628-257">자세한 내용은 [강력한 형식의 모델 및 @model 키워드](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="6c628-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="6c628-258">\@namespace</span></span>

<span data-ttu-id="6c628-259">`@namespace` 지시문:</span><span class="sxs-lookup"><span data-stu-id="6c628-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="6c628-260">생성된 Razor 페이지, MVC 보기 또는 Razor 구성 요소의 클래스에 대한 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="6c628-261">디렉터리 트리의 가장 가까운 파일, *_ViewImports.cshtml*(보기 또는 페이지) 또는 *_Imports.razor*(Razor 구성 요소)에서 페이지, 보기 또는 구성 요소 클래스의 루트 파생 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="6c628-262">Razor Pages 예제는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="6c628-263">각 페이지에서는 *Pages/_ViewImports.cshtml*을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="6c628-264">*Pages/_ViewImports.cshtml*에는 `@namespace Hello.World`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="6c628-265">각 페이지에는 `Hello.World`가 네임스페이스의 루트로 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="6c628-266">페이지</span><span class="sxs-lookup"><span data-stu-id="6c628-266">Page</span></span>                                        | <span data-ttu-id="6c628-267">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="6c628-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="6c628-268">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="6c628-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="6c628-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="6c628-271">위의 관계는 MVC 보기와 Razor 구성 요소에 사용되는 가져오기 파일에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="6c628-272">여러 가져오기 파일에 `@namespace` 지시문이 있으면 디렉터리 트리의 페이지, 보기 또는 구성 요소에 가장 가까운 파일을 사용하여 루트 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="6c628-273">이전 예제의 *EvenMorePages* 폴더에 `@namespace Another.Planet`이 포함된 가져오기 파일이 있으면(또는 *Pages/MorePages/EvenMorePages/Page.cshtml* 파일에 `@namespace Another.Planet` 포함), 다음 표에 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="6c628-274">페이지</span><span class="sxs-lookup"><span data-stu-id="6c628-274">Page</span></span>                                        | <span data-ttu-id="6c628-275">네임스페이스</span><span class="sxs-lookup"><span data-stu-id="6c628-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="6c628-276">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="6c628-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="6c628-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="6c628-279">\@page</span><span class="sxs-lookup"><span data-stu-id="6c628-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c628-280">`@page` 지시문은 표시되는 파일 형식에 따라 서로 다른 효과를 냅니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="6c628-281">지시문:</span><span class="sxs-lookup"><span data-stu-id="6c628-281">The directive:</span></span>

* <span data-ttu-id="6c628-282">*.cshtml* 파일에서 파일은 Razor 페이지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="6c628-283">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes) 및 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="6c628-284">Razor 구성 요소가 요청을 직접 처리하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="6c628-285">자세한 내용은 <xref:blazor/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6c628-286">*.cshtml* 파일의 첫 번째 줄에 있는 `@page` 지시문은 파일이 Razor 페이지임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="6c628-287">자세한 내용은 <xref:razor-pages/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="6c628-288">\@section</span><span class="sxs-lookup"><span data-stu-id="6c628-288">\@section</span></span>

<span data-ttu-id="6c628-289">*이 시나리오는 MVC 보기와 Razor Pages(cshtml)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="6c628-290">`@section` 지시문은 [MVC 및 Razor Pages 레이아웃](xref:mvc/views/layout)과 함께 사용되어 보기나 페이지에서 HTML 페이지의 여러 부분에 있는 콘텐츠를 렌더링할 수 있게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="6c628-291">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="6c628-292">\@using</span><span class="sxs-lookup"><span data-stu-id="6c628-292">\@using</span></span>

<span data-ttu-id="6c628-293">`@using` 지시문은 C# `using` 지시문을 생성된 보기에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c628-294">[Razor 구성 요소](xref:blazor/components)에서는 `@using` 범위 내에 있는 구성 요소를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="6c628-295">지시문 특성</span><span class="sxs-lookup"><span data-stu-id="6c628-295">Directive attributes</span></span>

<span data-ttu-id="6c628-296">Razor 지시문 특성은 기호 다음에 예약 된 키워드를 사용 하 여 암시적 식으로 표현 됩니다 `@` .</span><span class="sxs-lookup"><span data-stu-id="6c628-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="6c628-297">지시문 특성은 일반적으로 요소가 구문 분석 되거나 다른 기능을 사용 하도록 설정 하는 방식을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="6c628-298">\@attributes</span><span class="sxs-lookup"><span data-stu-id="6c628-298">\@attributes</span></span>

<span data-ttu-id="6c628-299">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-300">`@attributes`를 사용하면 구성 요소가 선언되지 않은 특성을 렌더링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="6c628-301">자세한 내용은 <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="6c628-302">\@bind</span><span class="sxs-lookup"><span data-stu-id="6c628-302">\@bind</span></span>

<span data-ttu-id="6c628-303">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-304">구성 요소의 데이터 바인딩은 `@bind` 특성을 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="6c628-305">자세한 내용은 <xref:blazor/data-binding>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="6c628-306">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="6c628-306">\@on{EVENT}</span></span>

<span data-ttu-id="6c628-307">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-308">Razor에서는 구성 요소에 대한 이벤트 처리 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="6c628-309">자세한 내용은 <xref:blazor/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="6c628-310">\@on{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="6c628-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="6c628-311">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-312">이벤트의 기본 작업을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="6c628-313">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="6c628-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="6c628-314">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-315">이벤트의 이벤트 전파를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="6c628-316">\@key</span><span class="sxs-lookup"><span data-stu-id="6c628-316">\@key</span></span>

<span data-ttu-id="6c628-317">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-318">`@key` 지시어 특성을 사용하면 구성 요소 diff 알고리즘이 키의 값에 따라 요소 또는 구성 요소를 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="6c628-319">자세한 내용은 <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="6c628-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="6c628-320">\@ref</span></span>

<span data-ttu-id="6c628-321">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-322">구성 요소 참조(`@ref`)에서는 해당 인스턴스에 대해 명령을 실행할 수 있도록 구성 요소 인스턴스를 참조하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="6c628-323">자세한 내용은 <xref:blazor/components#capture-references-to-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="6c628-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="6c628-324">\@typeparam</span></span>

<span data-ttu-id="6c628-325">*이 시나리오는 Razor 구성 요소(.razor)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="6c628-326">`@typeparam` 지시문은 생성된 구성 요소 클래스에 대한 제네릭 형식 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="6c628-327">자세한 내용은 <xref:blazor/templated-components#generic-typed-components>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="6c628-328">템플릿에 작성된 Razor 대리자</span><span class="sxs-lookup"><span data-stu-id="6c628-328">Templated Razor delegates</span></span>

<span data-ttu-id="6c628-329">Razor 템플릿을 사용하면 UI 코드 조각을 다음 형식으로 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="6c628-330">다음 예제에서는 템플릿에 작성된 Razor 대리자를 <xref:System.Func%602>로 지정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="6c628-331">[dynamic 형식](/dotnet/csharp/programming-guide/types/using-type-dynamic)은 대리자에서 캡슐화하는 메서드의 매개 변수로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="6c628-332">[object 형식](/dotnet/csharp/language-reference/keywords/object)은 대리자의 반환 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="6c628-333">템플릿은 `Name` 속성이 있는 `Pet`의 <xref:System.Collections.Generic.List%601>에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="6c628-334">템플릿은 `foreach` 문에서 제공하는 `pets`에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="6c628-335">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="6c628-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="6c628-336">또한 인라인 Razor 템플릿은 메서드에 대한 인수로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="6c628-337">다음 예제에서는 `Repeat` 메서드에서 Razor 템플릿을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="6c628-338">이 메서드는 템플릿을 사용하여 목록에서 제공된 항목의 반복이 포함된 HTML 콘텐츠를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="6c628-339">이전 예제의 애완 동물 목록을 사용하여 `Repeat` 메서드는 다음 항목과 함께 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="6c628-340"><xref:System.Collections.Generic.List%601>의 `Pet`입니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="6c628-341">각 애완 동물에 대한 반복 횟수</span><span class="sxs-lookup"><span data-stu-id="6c628-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="6c628-342">순서가 지정되지 않은 목록의 목록 항목에 사용할 인라인 템플릿</span><span class="sxs-lookup"><span data-stu-id="6c628-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="6c628-343">렌더링된 출력:</span><span class="sxs-lookup"><span data-stu-id="6c628-343">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="6c628-344">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="6c628-344">Tag Helpers</span></span>

<span data-ttu-id="6c628-345">*이 시나리오는 MVC 보기와 Razor Pages(cshtml)에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c628-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="6c628-346">[태그 도우미](xref:mvc/views/tag-helpers/intro)와 관련된 세 가지 지시문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="6c628-347">지시문</span><span class="sxs-lookup"><span data-stu-id="6c628-347">Directive</span></span> | <span data-ttu-id="6c628-348">기능</span><span class="sxs-lookup"><span data-stu-id="6c628-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="6c628-349">보기에 태그 도우미를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="6c628-350">보기에서 이전에 추가된 태그 도우미를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="6c628-351">태그 도우미를 지원하고 태그 도우미 사용을 명시적으로 만들어주는 태그 접두사를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="6c628-352">예약 된 키워드</span><span class="sxs-lookup"><span data-stu-id="6c628-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="6c628-353">어</span><span class="sxs-lookup"><span data-stu-id="6c628-353"> keywords</span></span>

* <span data-ttu-id="6c628-354">페이지(ASP.NET Core 2.1 이상 필요)</span><span class="sxs-lookup"><span data-stu-id="6c628-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="6c628-355">namespace</span><span class="sxs-lookup"><span data-stu-id="6c628-355">namespace</span></span>
* <span data-ttu-id="6c628-356">functions</span><span class="sxs-lookup"><span data-stu-id="6c628-356">functions</span></span>
* <span data-ttu-id="6c628-357">상속</span><span class="sxs-lookup"><span data-stu-id="6c628-357">inherits</span></span>
* <span data-ttu-id="6c628-358">model</span><span class="sxs-lookup"><span data-stu-id="6c628-358">model</span></span>
* <span data-ttu-id="6c628-359">section</span><span class="sxs-lookup"><span data-stu-id="6c628-359">section</span></span>
* <span data-ttu-id="6c628-360">도우미(현재 ASP.NET Core에서 지원되지 않음)</span><span class="sxs-lookup"><span data-stu-id="6c628-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="6c628-361">키워드는로 이스케이프 됩니다 `@(Razor Keyword)` (예: `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="6c628-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="6c628-362">C # Razor 키워드</span><span class="sxs-lookup"><span data-stu-id="6c628-362">C# Razor keywords</span></span>

* <span data-ttu-id="6c628-363">case</span><span class="sxs-lookup"><span data-stu-id="6c628-363">case</span></span>
* <span data-ttu-id="6c628-364">do</span><span class="sxs-lookup"><span data-stu-id="6c628-364">do</span></span>
* <span data-ttu-id="6c628-365">default</span><span class="sxs-lookup"><span data-stu-id="6c628-365">default</span></span>
* <span data-ttu-id="6c628-366">-</span><span class="sxs-lookup"><span data-stu-id="6c628-366">for</span></span>
* <span data-ttu-id="6c628-367">foreach</span><span class="sxs-lookup"><span data-stu-id="6c628-367">foreach</span></span>
* <span data-ttu-id="6c628-368">if</span><span class="sxs-lookup"><span data-stu-id="6c628-368">if</span></span>
* <span data-ttu-id="6c628-369">else</span><span class="sxs-lookup"><span data-stu-id="6c628-369">else</span></span>
* <span data-ttu-id="6c628-370">lock</span><span class="sxs-lookup"><span data-stu-id="6c628-370">lock</span></span>
* <span data-ttu-id="6c628-371">switch</span><span class="sxs-lookup"><span data-stu-id="6c628-371">switch</span></span>
* <span data-ttu-id="6c628-372">다음을 시도해 보세요.</span><span class="sxs-lookup"><span data-stu-id="6c628-372">try</span></span>
* <span data-ttu-id="6c628-373">catch</span><span class="sxs-lookup"><span data-stu-id="6c628-373">catch</span></span>
* <span data-ttu-id="6c628-374">finally</span><span class="sxs-lookup"><span data-stu-id="6c628-374">finally</span></span>
* <span data-ttu-id="6c628-375">using</span><span class="sxs-lookup"><span data-stu-id="6c628-375">using</span></span>
* <span data-ttu-id="6c628-376">while</span><span class="sxs-lookup"><span data-stu-id="6c628-376">while</span></span>

<span data-ttu-id="6c628-377">C # Razor 키워드는를 사용 하 여 이중 이스케이프 되어야 합니다 `@(@C# Razor Keyword)` (예: `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="6c628-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="6c628-378">첫 번째는 `@` 파서를 이스케이프 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="6c628-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="6c628-379">두 번째 `@`은 C# 파서를 이스케이프합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="6c628-380">에서 사용 되지 않는 예약 된 키워드Razor</span><span class="sxs-lookup"><span data-stu-id="6c628-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="6c628-381">class</span><span class="sxs-lookup"><span data-stu-id="6c628-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="6c628-382">Razor뷰에 대해 생성 된 c # 클래스를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6c628-383">.NET Core SDK 2.1 이상에서 [ Razor SDK](xref:razor-pages/sdk) 는 파일의 컴파일을 처리 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="6c628-384">프로젝트를 빌드할 때 SDK는 Razor 프로젝트 루트에서 *>/<target_framework_moniker>/ Razor 디렉터리 build_configuration obj/<* 를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="6c628-385">디렉터리 내의 디렉터리 구조는 *Razor* 프로젝트의 디렉터리 구조를 미러링합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="6c628-386">.NET Core 2.1를 대상으로 하는 ASP.NET Core 2.1 페이지 프로젝트에서 다음 디렉터리 구조를 고려 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="6c628-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="6c628-387">**영역인**</span><span class="sxs-lookup"><span data-stu-id="6c628-387">**Areas/**</span></span>
  * <span data-ttu-id="6c628-388">**지점**</span><span class="sxs-lookup"><span data-stu-id="6c628-388">**Admin/**</span></span>
    * <span data-ttu-id="6c628-389">**마주보**</span><span class="sxs-lookup"><span data-stu-id="6c628-389">**Pages/**</span></span>
      * <span data-ttu-id="6c628-390">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="6c628-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="6c628-392">**마주보**</span><span class="sxs-lookup"><span data-stu-id="6c628-392">**Pages/**</span></span>
  * <span data-ttu-id="6c628-393">**공유할**</span><span class="sxs-lookup"><span data-stu-id="6c628-393">**Shared/**</span></span>
    * <span data-ttu-id="6c628-394">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="6c628-395">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="6c628-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="6c628-397">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6c628-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="6c628-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="6c628-399">*Debug* 구성에 프로젝트를 빌드하면 다음 *obj* 디렉터리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="6c628-400">**인스턴스인**</span><span class="sxs-lookup"><span data-stu-id="6c628-400">**obj/**</span></span>
  * <span data-ttu-id="6c628-401">**디버그**</span><span class="sxs-lookup"><span data-stu-id="6c628-401">**Debug/**</span></span>
    * <span data-ttu-id="6c628-402">**netcoreapp1.0 2.1/**</span><span class="sxs-lookup"><span data-stu-id="6c628-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="6c628-403">**영역인**</span><span class="sxs-lookup"><span data-stu-id="6c628-403">**Areas/**</span></span>
          * <span data-ttu-id="6c628-404">**지점**</span><span class="sxs-lookup"><span data-stu-id="6c628-404">**Admin/**</span></span>
            * <span data-ttu-id="6c628-405">**마주보**</span><span class="sxs-lookup"><span data-stu-id="6c628-405">**Pages/**</span></span>
              * <span data-ttu-id="6c628-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="6c628-407">**마주보**</span><span class="sxs-lookup"><span data-stu-id="6c628-407">**Pages/**</span></span>
          * <span data-ttu-id="6c628-408">**공유할**</span><span class="sxs-lookup"><span data-stu-id="6c628-408">**Shared/**</span></span>
            * <span data-ttu-id="6c628-409">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="6c628-410">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="6c628-411">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="6c628-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="6c628-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="6c628-413">*Pages/Index. cshtml*에 대해 생성 된 클래스를 보려면 *obj/Debug/Netcoreapp1.0 2.1/ Razor /Pages/Index.g.cshtml.cs*를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="6c628-414">ASP.NET Core MVC 프로젝트에 다음 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="6c628-415">`Startup.ConfigureServices`에서 MVC가 추가한 `RazorTemplateEngine`을 `CustomTemplateEngine` 클래스로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="6c628-416">`CustomTemplateEngine`의 `return csharpDocument;` 문에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="6c628-417">중단점에서 프로그램 실행이 중지되면 `generatedCode`의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode의 텍스트 시각화 도우미 보기](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="6c628-419">보기 조회 및 대/소문자 구분</span><span class="sxs-lookup"><span data-stu-id="6c628-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="6c628-420">Razor뷰 엔진은 뷰에 대해 대/소문자를 구분 하는 조회를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="6c628-421">그러나 실제 조회는 기본 파일 시스템에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="6c628-422">파일 기반 원본:</span><span class="sxs-lookup"><span data-stu-id="6c628-422">File based source:</span></span>
  * <span data-ttu-id="6c628-423">파일 시스템이 대/소문자를 구문하지 않은 운영 체제(예: Windows)에서는 실제 파일 공급자 조회에서 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="6c628-424">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*, */Views/home/test.cshtml* 및 기타 대/소문자 구분 변형과 일치하는 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="6c628-425">대/소문자를 구분하는 파일 시스템(예: Linux, OSX 및 `EmbeddedFileProvider`를 사용하는 파일 시스템)에서는 조회 시 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="6c628-426">예를 들어 `return View("Test")`는 */Views/Home/Test.cshtml*과 정확히 일치하는 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="6c628-427">미리 컴파일된 보기: ASP.NET 2.0 이상을 사용하는 경우 모든 운영 체제에서 미리 컴파일된 보기 조회 시 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="6c628-428">이 동작은 Windows의 물리적 파일 공급자 동작과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="6c628-429">미리 컴파일된 두 보기의 대소문자만 다른 경우 조회 결과가 불명확합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="6c628-430">개발자는 파일 및 디렉터리 이름의 대/소문자를 다음의 대/소문자와 매칭하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="6c628-431">영역, 컨트롤러 및 작업 이름.</span><span class="sxs-lookup"><span data-stu-id="6c628-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="6c628-432">마주보.</span><span class="sxs-lookup"><span data-stu-id="6c628-432"> Pages.</span></span>

<span data-ttu-id="6c628-433">대/소문자를 일치시키면 배포 시 기본 파일 시스템에 관계 없이 해당 보기를 잘 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c628-434">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6c628-434">Additional resources</span></span>

<span data-ttu-id="6c628-435">을 [사용 하는 Razor ASP.NET 웹 프로그래밍 소개 구문은](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) 구문을 사용한 프로그래밍의 많은 예제를 제공 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c628-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
