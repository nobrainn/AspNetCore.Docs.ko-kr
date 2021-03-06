---
title: ASP.NET Core에서 XSS (교차 사이트 스크립팅) 방지
author: rick-anderson
description: ASP.NET Core 앱에서이 취약점을 해결 하는 데 필요한 XSS (사이트 간 스크립팅) 및 기술에 대해 알아봅니다.
ms.author: riande
ms.date: 10/02/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cross-site-scripting
ms.openlocfilehash: 24fab313c3af30cfd4143ba29a33ba25bfcdf9a9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021811"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core에서 XSS (교차 사이트 스크립팅) 방지

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

XSS (교차 사이트 스크립팅)는 공격자가 클라이언트 쪽 스크립트 (일반적으로 JavaScript)를 웹 페이지에 넣을 수 있도록 하는 보안 취약점입니다. 다른 사용자가 영향을 받는 페이지를 로드 하면 공격자의 스크립트가 실행 되어 공격자가 cookie 및 세션 토큰을 도용 하거나 DOM 조작을 통해 웹 페이지의 내용을 변경 하거나 브라우저를 다른 페이지로 리디렉션할 수 있습니다. XSS 취약점은 일반적으로 응용 프로그램에서 사용자 입력을 가져와 유효성 검사, 인코딩 또는 이스케이프 하지 않고 페이지에 출력할 때 발생 합니다.

## <a name="protecting-your-application-against-xss"></a>XSS 로부터 응용 프로그램 보호

기본 수준의 XSS는 응용 프로그램을 속여 하 여 `<script>` 렌더링 된 페이지에 태그를 삽입 하거나 요소에 이벤트를 삽입 하는 방식으로 작동 `On*` 합니다. 개발자는 응용 프로그램에 XSS를 도입 하지 않도록 다음 방지 단계를 사용 해야 합니다.

1. 아래 나머지 단계를 수행 하지 않으면 HTML 입력에 신뢰할 수 없는 데이터를 넣지 마세요. 신뢰할 수 없는 데이터는 공격자가 제어 하는 데이터 (HTML 양식 입력, 쿼리 문자열, HTTP 헤더)가 응용 프로그램을 위반할 수 없는 경우에도 데이터베이스를 위반할 수 있습니다.

2. HTML 요소 내에 신뢰할 수 없는 데이터를 넣기 전에 HTML로 인코딩해야 합니다. HTML 인코딩은와 같은 문자를 사용 하 여,와 같은 &lt; 안전한 형식으로 변경 합니다 &amp; .

3. 신뢰할 수 없는 데이터를 HTML 특성에 배치 하기 전에 HTML로 인코딩해야 합니다. HTML 특성 인코딩은 HTML 인코딩의 상위 집합이 며 "and"와 같은 추가 문자를 인코딩합니다.

4. 신뢰할 수 없는 데이터를 JavaScript에 배치 하기 전에 런타임에 검색 하는 콘텐츠를 포함 하는 HTML 요소에 데이터를 배치 합니다. 가능 하지 않은 경우 데이터가 JavaScript로 인코딩 되었는지 확인 합니다. JavaScript 인코딩은 JavaScript에 대 한 위험한 문자를 사용 하 여 16 진수로 바꿉니다. 예를 들어 &lt; 는로 인코딩됩니다 `\u003C` .

5. 신뢰할 수 없는 데이터를 URL 쿼리 문자열에 배치 하기 전에 URL이 인코딩 되는지 확인 합니다.

## <a name="html-encoding-using-no-locrazor"></a>HTML 인코딩 사용Razor

RazorMVC에서 사용 하는 엔진은이를 방지 하기 위해 정말로 작업 하지 않는 한 변수에서 모든 출력을 자동으로 인코딩합니다. 지시문을 사용할 때마다 HTML 특성 인코딩 규칙을 사용 *@* 합니다. Html 특성 인코딩은 HTML 인코딩의 상위 집합 이기 때문에 HTML 인코딩 또는 HTML 특성 인코딩을 사용 해야 하는지 여부를 걱정 하지 않아도 됩니다. 신뢰할 수 없는 입력을 JavaScript에 직접 삽입 하려고 할 때가 아니라 HTML 컨텍스트에서만 @를 사용 하도록 해야 합니다. 태그 도우미는 태그 매개 변수에서 사용 하는 입력도 인코딩합니다.

다음 뷰를 사용 합니다 Razor .

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

이 보기는 *Untrustedinput* 변수의 내용을 출력 합니다. 이 변수에는 XSS 공격에 사용 되는 일부 문자 (즉 &lt; , "및")가 포함 됩니다 &gt; . 소스를 검사 하면 렌더링 된 출력이 다음과 같이 표시 됩니다.

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC는 `HtmlString` 출력 시 자동으로 인코딩되지 않는 클래스를 제공 합니다. 이는 XSS 취약성을 노출 하므로 신뢰할 수 없는 입력과 함께 사용 하면 안 됩니다.

## <a name="javascript-encoding-using-no-locrazor"></a>JavaScript Encoding 사용Razor

JavaScript에 값을 삽입 하 여 보기에서 처리할 수 있는 경우가 있을 수 있습니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다. 값을 삽입 하는 가장 안전한 방법은 태그의 데이터 특성에 값을 추가 하 고 JavaScript에서 검색 하는 것입니다. 예:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

그러면 다음 HTML이 생성 됩니다.

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

실행 될 때 다음을 렌더링 합니다.

```
<"123">
   <"123">
```

JavaScript 인코더를 직접 호출할 수도 있습니다.

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

이렇게 하면 브라우저에서 다음과 같이 렌더링 됩니다.

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> JavaScript에서 신뢰할 수 없는 입력을 연결 하 여 DOM 요소를 만들지 마세요. 과 `createElement()` 같이 속성 값을 적절 하 게 사용 및 할당 `node.TextContent=` 하거나, `element.SetAttribute()` / `element[attribute]=` 그렇지 않으면 DOM 기반 XSS에 직접 노출 합니다.

## <a name="accessing-encoders-in-code"></a>코드에서 인코더 액세스

HTML, JavaScript 및 URL 인코더를 두 가지 방법으로 사용할 수 있습니다. [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 코드를 삽입 하거나 네임 스페이스에 포함 된 기본 인코더를 사용할 수 있습니다 `System.Text.Encodings.Web` . 기본 인코더를 사용 하는 경우 안전 하지 않은 것으로 간주 되는 문자 범위에 적용 된 모든 것이 적용 되지 않습니다. 기본 인코더는 가능한 가장 안전한 인코딩 규칙을 사용 합니다.

DI를 통해 구성 가능한 인코더를 사용 하려면 생성자는 *htmlencoder*, *JavaScriptEncoder* 및 *urlencoder* 매개 변수를 적절 하 게 사용 해야 합니다. 예를 들면 다음과 같습니다.

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>인코딩 URL 매개 변수

신뢰할 수 없는 입력을 값으로 사용 하 여 URL 쿼리 문자열을 작성 하려는 경우를 사용 `UrlEncoder` 하 여 값을 인코딩합니다. 예를 들면 다음과 같습니다.

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

인코딩 후 Url-encodedvalue 변수에는가 포함 됩니다 `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . 공백, 따옴표, 문장 부호 및 기타 안전 하지 않은 문자는 16 진수 값으로 인코딩됩니다. 예를 들어 공백 문자는 %20이 됩니다.

>[!WARNING]
> URL 경로의 일부로는 신뢰할 수 없는 입력을 사용 하지 마세요. 항상 신뢰할 수 없는 입력을 쿼리 문자열 값으로 전달 합니다.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>인코더 사용자 지정

기본적으로 인코더는 기본 라틴어 유니코드 범위로 제한 된 안전 목록을 사용 하 고 해당 범위 밖의 모든 문자를 해당 문자 코드로 인코딩합니다. 이 동작은 Razor 인코더를 사용 하 여 문자열을 출력 하는 TagHelper 및 HtmlHelper 렌더링에도 영향을 줍니다.

이를 바탕으로 하는 이유는 알려지지 않은 또는 향후 브라우저 버그를 방지 하는 것입니다 (이전 브라우저 버그는 영어가 아닌 문자 처리를 기반으로 구문 분석을 수행 함). 웹 사이트에서 중국어, 키릴 자모 또는 다른 사용자와 같은 라틴 문자가 아닌 문자를 많이 사용 하는 경우이는 원하는 동작이 아닐 수 있습니다.

에서 시작 하는 동안 응용 프로그램에 적합 한 유니코드 범위를 포함 하도록 인코더 안전 목록을 사용자 지정할 수 있습니다 `ConfigureServices()` .

예를 들어 기본 구성을 사용 하는 경우 Razor 와 같은 htmlhelper를 사용할 수 있습니다.

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

웹 페이지의 소스를 볼 때 중국어 텍스트가 인코딩된 상태로 다음과 같이 렌더링 된 것을 볼 수 있습니다.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

인코더가 안전 하 게 처리 하는 문자를 확대 하려면의 메서드에 다음 줄을 삽입 합니다 `ConfigureServices()` `startup.cs` .

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

이 예에서는 CjkUnifiedIdeographs 유니코드 범위를 포함 하도록 안전 목록을 확대 합니다. 이제 렌더링 된 출력이 다음과 같은 상태가 됩니다.

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

안전 목록 범위는 언어가 아닌 유니코드 코드 차트로 지정 됩니다. [유니코드 표준](https://unicode.org/) 에는 문자를 포함 하는 차트를 찾는 데 사용할 수 있는 [코드 차트](https://www.unicode.org/charts/index.html) 목록이 있습니다. 각 인코더, Html, JavaScript 및 Url은 별도로 구성 해야 합니다.

> [!NOTE]
> Safe 목록의 사용자 지정은 DI를 통해 원본 인코더에만 영향을 줍니다. 기본값을 통해 인코더에 직접 액세스 하는 경우 기본 `System.Text.Encodings.Web.*Encoder.Default` 라틴어 전용 safelist 사용 됩니다.

## <a name="where-should-encoding-take-place"></a>인코딩 수행 위치

일반적으로 허용 되는 방법은 인코딩이 출력 지점에서 발생 하 고 인코딩된 값이 데이터베이스에 저장 되지 않아야 한다는 것입니다. 출력 지점에서 인코딩을 사용 하면 HTML에서 쿼리 문자열 값과 같은 데이터 사용을 변경할 수 있습니다. 또한 검색 전에 값을 인코딩하지 않고 인코더에 대 한 변경 내용 또는 버그 수정을 활용할 수 있도록 하 여 데이터를 쉽게 검색할 수 있습니다.

## <a name="validation-as-an-xss-prevention-technique"></a>XSS 방지 기술로 유효성 검사

유효성 검사는 XSS 공격을 제한 하는 데 유용한 도구 일 수 있습니다. 예를 들어 0-9 문자만 포함 하는 숫자 문자열은 XSS 공격을 트리거하지 않습니다. 사용자 입력에서 HTML을 허용할 때 유효성 검사가 더 복잡해 집니다. HTML 입력을 구문 분석 하기가 어렵습니다. 포함 된 HTML을 제거 하는 Markdown와 결합 된는 다양 한 입력을 허용 하는 안전한 옵션입니다. 유효성 검사만 사용 하지 않습니다. 유효성 검사 또는 삭제가 수행 되었는지 여부에 관계 없이 항상 신뢰할 수 없는 입력을 출력 전에 인코딩합니다.
