---
title: 'title: ASP.NET Core의 전역화 및 지역화 작성자: rick-anderson 설명: ASP.NET Core에서 다른 언어와 문화권으로의 콘텐츠 지역화를 위한 서비스 및 미들웨어를 제공하는 방법을 알아봅니다.'
author: rick-anderson
description: 'ms.author: riande ms.date: 11/30/2019 no-loc:'
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: e3b73a7a559d2f4a0803dc26dd42257c60fab884
ms.sourcegitcommit: e95b90585a9bc353f57643ed7d8e652dc498359b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84356962"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="dd679-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd679-103">'Blazor'</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="dd679-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd679-104">'Identity'</span></span>

<span data-ttu-id="dd679-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd679-105">'Let's Encrypt'</span></span> <span data-ttu-id="dd679-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd679-106">'Razor'</span></span>

<span data-ttu-id="dd679-107">'SignalR' uid: fundamentals/localization</span><span class="sxs-lookup"><span data-stu-id="dd679-107">'SignalR' uid: fundamentals/localization</span></span> <span data-ttu-id="dd679-108">ASP.NET Core에서 세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-108">Globalization and localization in ASP.NET Core</span></span> <span data-ttu-id="dd679-109">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="dd679-109">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="dd679-110">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-110">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="dd679-111">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-111">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="dd679-112">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-112">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="dd679-113">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-113">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="dd679-114">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-114">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="dd679-115">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-115">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="dd679-116">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-116">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="dd679-117">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-117">App localization involves the following:</span></span>

<span data-ttu-id="dd679-118">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-118">Make the app's content localizable</span></span> <span data-ttu-id="dd679-119">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-119">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-120">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-120">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-121">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd679-121">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="dd679-122">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-122">Make the app's content localizable</span></span> <span data-ttu-id="dd679-123"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-123"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="dd679-124">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-124">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="dd679-125">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-125">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="dd679-126">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-126">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="dd679-127">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-127">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="dd679-128">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-128">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="dd679-129">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-129">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="dd679-130">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-130">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="dd679-131">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-131">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="dd679-132">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-132">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="dd679-133">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-133">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="dd679-134">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-134">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="dd679-135">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-135">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="dd679-136">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-136">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="dd679-137">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-137">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="dd679-138">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-138">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="dd679-139">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-139">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="dd679-140">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-140">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="dd679-141">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-141">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="dd679-142">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-142">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="dd679-143">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-143">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="dd679-144">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-144">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="dd679-145">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-145">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="dd679-146">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="dd679-146">View localization</span></span> <span data-ttu-id="dd679-147">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-147">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="dd679-148">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-148">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="dd679-149">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-149">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="dd679-150">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-150">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="dd679-151">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-151">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="dd679-152">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-152">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="dd679-153">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-153">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="dd679-154">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-154">Consider the following Razor markup:</span></span>

<span data-ttu-id="dd679-155">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-155">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="dd679-156">Key</span><span class="sxs-lookup"><span data-stu-id="dd679-156">Key</span></span>

<span data-ttu-id="dd679-157">값</span><span class="sxs-lookup"><span data-stu-id="dd679-157">Value</span></span> <span data-ttu-id="dd679-158">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-158">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="dd679-159">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-159">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="dd679-160">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-160">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="dd679-161">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-161">DataAnnotations localization</span></span> <span data-ttu-id="dd679-162">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-162">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="dd679-163">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-163">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="dd679-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="dd679-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="dd679-166">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-166">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="dd679-167">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="dd679-167">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="dd679-168">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-168">Using one resource string for multiple classes</span></span>

<span data-ttu-id="dd679-169">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-169">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="dd679-170">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-170">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="dd679-171">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-171">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="dd679-172">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-172">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-173">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="dd679-173">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="dd679-174">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-174">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="dd679-175">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-175">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="dd679-176">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-176">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="dd679-177">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-177">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="dd679-178">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-178">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="dd679-179">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-179">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="dd679-180">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-180">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="dd679-181">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-181">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="dd679-182">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-182">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="dd679-183">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="dd679-183">Resource files</span></span>

1. <span data-ttu-id="dd679-184">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-184">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.](localization/_static/newi.png)

2. <span data-ttu-id="dd679-187">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-187">"es" is the language code for Spanish.</span></span>

    ![Visual Studio에서 이 리소스 파일을 만들려면:](localization/_static/res.png)

3. <span data-ttu-id="dd679-189">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-189">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/hola.png)

    <span data-ttu-id="dd679-191">두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-191">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="dd679-193">새 항목 추가 대화 상자</span><span class="sxs-lookup"><span data-stu-id="dd679-193">Add New Item dialog</span></span>

<span data-ttu-id="dd679-194">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-194">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="dd679-195">이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)</span><span class="sxs-lookup"><span data-stu-id="dd679-195">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="dd679-196">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-196">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="dd679-197">Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기</span><span class="sxs-lookup"><span data-stu-id="dd679-197">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="dd679-198">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="dd679-198">Resource file naming</span></span>

<span data-ttu-id="dd679-199">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-199">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="dd679-200">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-200">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="dd679-201">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-201">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="dd679-202">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-202">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="dd679-203">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-203">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="dd679-204">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-204">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="dd679-205">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-205">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="dd679-206">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-206">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="dd679-207">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-207">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="dd679-208">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-208">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="dd679-209">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-209">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="dd679-210">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="dd679-210">Resource name</span></span> |
|    |     |

<span data-ttu-id="dd679-211">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="dd679-211">Dot or path naming</span></span> <span data-ttu-id="dd679-212">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-212">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="dd679-213">점</span><span class="sxs-lookup"><span data-stu-id="dd679-213">Dot</span></span> <span data-ttu-id="dd679-214">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-214">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="dd679-215">경로</span><span class="sxs-lookup"><span data-stu-id="dd679-215">Path</span></span>

* <span data-ttu-id="dd679-216">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-216">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="dd679-217">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-217">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="dd679-218"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-218"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="dd679-219">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-219">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="dd679-220">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-220">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="dd679-221">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-221">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="dd679-222">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-222">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="dd679-223">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="dd679-223">RootNamespaceAttribute</span></span>
* <span data-ttu-id="dd679-224">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-224">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="dd679-225">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-225">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="dd679-226">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-226">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="dd679-227">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="dd679-227">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="dd679-228">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-228">Localization does not work by default.</span></span>

<span data-ttu-id="dd679-229">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-229">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="dd679-230">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-230">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="dd679-231">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="dd679-231">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="dd679-232">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-232">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="dd679-233">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="dd679-233">Culture fallback behavior</span></span> <span data-ttu-id="dd679-234">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-234">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="dd679-235">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-235">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="dd679-236">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-236">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="dd679-237">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-237">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="dd679-238">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-238">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="dd679-239">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-239">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="dd679-240">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-240">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="dd679-241">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-241">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="dd679-242">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-242">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="dd679-243">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-243">*Welcome.fr.resx*</span></span>

<span data-ttu-id="dd679-244">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="dd679-244">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="dd679-245">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-245">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="dd679-246">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-246">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="dd679-247">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-247">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="dd679-248">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="dd679-248">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="dd679-249">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-249">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="dd679-250">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-250">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="dd679-251">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-251">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="dd679-252">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="dd679-252">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="dd679-253">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-253">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="dd679-254">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="dd679-254">Add other cultures</span></span>

<span data-ttu-id="dd679-255">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-255">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="dd679-256">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="dd679-256">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="dd679-257">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-257">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="dd679-258">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-258">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-259">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="dd679-259">Configure localization</span></span> <span data-ttu-id="dd679-260">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-260">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="dd679-261">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-261">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="dd679-262">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-262">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="dd679-263">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-263">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="dd679-264">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-264">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="dd679-265">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-265">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="dd679-266">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-266">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="dd679-267">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="dd679-267">Localization middleware</span></span> <span data-ttu-id="dd679-268">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-268">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="dd679-269">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-269">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="dd679-270">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="dd679-270">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="dd679-271">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-271">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="dd679-272">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-272">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="dd679-273">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-273">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="dd679-274">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-274">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="dd679-275">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-275">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="dd679-276">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-276">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="dd679-277">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-277">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="dd679-278">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-278">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="dd679-279">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-279">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="dd679-280">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-280">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="dd679-281">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-281">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="dd679-282">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-282">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="dd679-283">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-283">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="dd679-284">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-284">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="dd679-285">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-285">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="dd679-286">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-286">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="dd679-287">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-287">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="dd679-288">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-288">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="dd679-289">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-289">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="dd679-290">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-290">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="dd679-291">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-291">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="dd679-292">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="dd679-292">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="dd679-293">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-293">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="dd679-294">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-294">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).](localization/_static/lang.png)

3. <span data-ttu-id="dd679-296">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-296">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="dd679-297">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-297">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="dd679-298">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-298">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="dd679-299">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-299">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="dd679-300">인터넷 옵션</span><span class="sxs-lookup"><span data-stu-id="dd679-300">Internet Options</span></span>

<span data-ttu-id="dd679-301">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-301">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="dd679-302">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-302">Tap **Add a language**.</span></span> <span data-ttu-id="dd679-303">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-303">Add the language.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="dd679-304">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-304">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="dd679-305">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-305">Use a custom provider</span></span>

<span data-ttu-id="dd679-306">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-306">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="dd679-307">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-307">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="dd679-308">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-308">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="dd679-309">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-309">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="dd679-310">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-310">Set the culture programmatically</span></span> <span data-ttu-id="dd679-311">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-311">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="dd679-312">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-312">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="dd679-313">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-313">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="dd679-314">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-314">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="dd679-315">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-315">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="dd679-316">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-316">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="dd679-317">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="dd679-317">Model binding route data and query strings</span></span>

<span data-ttu-id="dd679-318">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-318">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="dd679-319">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="dd679-319">Globalization and localization terms</span></span> <span data-ttu-id="dd679-320">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-320">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="dd679-321">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-321">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="dd679-322">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-322">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="dd679-323">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-323">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="dd679-324">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-324">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="dd679-325">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-325">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="dd679-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="dd679-327">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-327">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="dd679-328">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-328">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="dd679-329">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-329">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="dd679-330">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-330">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="dd679-331">용어:</span><span class="sxs-lookup"><span data-stu-id="dd679-331">Terms:</span></span> <span data-ttu-id="dd679-332">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-332">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="dd679-333">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-333">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="dd679-334">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-334">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="dd679-335">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-335">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="dd679-336">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-336">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="dd679-337">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="dd679-337">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd679-338">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-338">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="dd679-339">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="dd679-339">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="dd679-340">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-340">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="dd679-341">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="dd679-341">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="dd679-342">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-342">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="dd679-343">추가 자료</span><span class="sxs-lookup"><span data-stu-id="dd679-343">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd679-344">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-344">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="dd679-345">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-345">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="dd679-346">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="dd679-346">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="dd679-347">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="dd679-347">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="dd679-348">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="dd679-348">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="dd679-349">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="dd679-349">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="dd679-350">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-350">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="dd679-351">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-351">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="dd679-352">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-352">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="dd679-353">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-353">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="dd679-354">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-354">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="dd679-355">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-355">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="dd679-356">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-356">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="dd679-357">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-357">App localization involves the following:</span></span>

<span data-ttu-id="dd679-358">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-358">Make the app's content localizable</span></span> <span data-ttu-id="dd679-359">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-359">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-360">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-360">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-361">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd679-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="dd679-362">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-362">Make the app's content localizable</span></span> <span data-ttu-id="dd679-363"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-363"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="dd679-364">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-364">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="dd679-365">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-365">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="dd679-366">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-366">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="dd679-367">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-367">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="dd679-368">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-368">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="dd679-369">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-369">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="dd679-370">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-370">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="dd679-371">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-371">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="dd679-372">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-372">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="dd679-373">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-373">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="dd679-374">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-374">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="dd679-375">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-375">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="dd679-376">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-376">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="dd679-377">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-377">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="dd679-378">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-378">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="dd679-379">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-379">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="dd679-380">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-380">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="dd679-381">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-381">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="dd679-382">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-382">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="dd679-383">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-383">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="dd679-384">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-384">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="dd679-385">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-385">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="dd679-386">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="dd679-386">View localization</span></span> <span data-ttu-id="dd679-387">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-387">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="dd679-388">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-388">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="dd679-389">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-389">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="dd679-390">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-390">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="dd679-391">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-391">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="dd679-392">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-392">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="dd679-393">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-393">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="dd679-394">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-394">Consider the following Razor markup:</span></span>

<span data-ttu-id="dd679-395">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-395">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="dd679-396">Key</span><span class="sxs-lookup"><span data-stu-id="dd679-396">Key</span></span>

<span data-ttu-id="dd679-397">값</span><span class="sxs-lookup"><span data-stu-id="dd679-397">Value</span></span> <span data-ttu-id="dd679-398">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-398">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="dd679-399">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-399">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="dd679-400">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-400">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="dd679-401">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-401">DataAnnotations localization</span></span> <span data-ttu-id="dd679-402">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-402">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="dd679-403">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-403">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="dd679-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="dd679-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="dd679-406">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-406">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="dd679-407">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="dd679-407">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="dd679-408">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-408">Using one resource string for multiple classes</span></span>

<span data-ttu-id="dd679-409">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-409">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="dd679-410">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-410">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="dd679-411">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-411">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="dd679-412">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-412">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-413">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="dd679-413">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="dd679-414">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-414">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="dd679-415">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-415">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="dd679-416">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-416">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="dd679-417">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-417">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="dd679-418">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-418">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="dd679-419">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-419">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="dd679-420">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-420">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="dd679-421">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-421">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="dd679-422">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-422">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="dd679-423">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="dd679-423">Resource files</span></span>

1. <span data-ttu-id="dd679-424">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-424">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.](localization/_static/newi.png)

2. <span data-ttu-id="dd679-427">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-427">"es" is the language code for Spanish.</span></span>

    ![Visual Studio에서 이 리소스 파일을 만들려면:](localization/_static/res.png)

3. <span data-ttu-id="dd679-429">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-429">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/hola.png)

    <span data-ttu-id="dd679-431">두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-431">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="dd679-433">새 항목 추가 대화 상자</span><span class="sxs-lookup"><span data-stu-id="dd679-433">Add New Item dialog</span></span>

<span data-ttu-id="dd679-434">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-434">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="dd679-435">이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)</span><span class="sxs-lookup"><span data-stu-id="dd679-435">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="dd679-436">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-436">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="dd679-437">Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기</span><span class="sxs-lookup"><span data-stu-id="dd679-437">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="dd679-438">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="dd679-438">Resource file naming</span></span>

<span data-ttu-id="dd679-439">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-439">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="dd679-440">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-440">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="dd679-441">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-441">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="dd679-442">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-442">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="dd679-443">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-443">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="dd679-444">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-444">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="dd679-445">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-445">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="dd679-446">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-446">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="dd679-447">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-447">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="dd679-448">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-448">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="dd679-449">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-449">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="dd679-450">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="dd679-450">Resource name</span></span> |
|    |     |

<span data-ttu-id="dd679-451">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="dd679-451">Dot or path naming</span></span> <span data-ttu-id="dd679-452">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-452">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="dd679-453">점</span><span class="sxs-lookup"><span data-stu-id="dd679-453">Dot</span></span> <span data-ttu-id="dd679-454">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-454">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="dd679-455">경로</span><span class="sxs-lookup"><span data-stu-id="dd679-455">Path</span></span>

* <span data-ttu-id="dd679-456">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-456">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="dd679-457">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-457">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="dd679-458"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-458"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="dd679-459">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-459">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="dd679-460">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-460">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="dd679-461">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-461">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="dd679-462">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-462">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="dd679-463">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="dd679-463">RootNamespaceAttribute</span></span>
* <span data-ttu-id="dd679-464">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-464">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="dd679-465">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-465">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="dd679-466">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-466">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="dd679-467">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="dd679-467">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="dd679-468">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-468">Localization does not work by default.</span></span>

<span data-ttu-id="dd679-469">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-469">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="dd679-470">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-470">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="dd679-471">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="dd679-471">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="dd679-472">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-472">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="dd679-473">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="dd679-473">Culture fallback behavior</span></span> <span data-ttu-id="dd679-474">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-474">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="dd679-475">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-475">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="dd679-476">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-476">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="dd679-477">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-477">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="dd679-478">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-478">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="dd679-479">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-479">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="dd679-480">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-480">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="dd679-481">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-481">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="dd679-482">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-482">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="dd679-483">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-483">*Welcome.fr.resx*</span></span>

<span data-ttu-id="dd679-484">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="dd679-484">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="dd679-485">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-485">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="dd679-486">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-486">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="dd679-487">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-487">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="dd679-488">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="dd679-488">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="dd679-489">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-489">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="dd679-490">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-490">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="dd679-491">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-491">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="dd679-492">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="dd679-492">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="dd679-493">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-493">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="dd679-494">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="dd679-494">Add other cultures</span></span>

<span data-ttu-id="dd679-495">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-495">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="dd679-496">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="dd679-496">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="dd679-497">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-497">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="dd679-498">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-498">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-499">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="dd679-499">Configure localization</span></span> <span data-ttu-id="dd679-500">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-500">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="dd679-501">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-501">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="dd679-502">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-502">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="dd679-503">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-503">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="dd679-504">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-504">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="dd679-505">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-505">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="dd679-506">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-506">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="dd679-507">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="dd679-507">Localization middleware</span></span> <span data-ttu-id="dd679-508">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-508">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="dd679-509">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-509">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="dd679-510">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="dd679-510">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="dd679-511">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-511">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="dd679-512">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-512">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="dd679-513">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-513">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="dd679-514">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-514">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="dd679-515">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-515">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="dd679-516">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-516">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="dd679-517">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-517">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="dd679-518">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-518">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="dd679-519">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-519">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="dd679-520">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-520">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="dd679-521">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-521">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="dd679-522">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-522">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="dd679-523">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-523">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="dd679-524">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-524">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="dd679-525">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-525">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="dd679-526">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-526">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="dd679-527">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-527">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="dd679-528">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-528">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="dd679-529">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-529">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="dd679-530">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-530">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="dd679-531">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-531">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="dd679-532">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="dd679-532">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="dd679-533">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-533">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="dd679-534">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-534">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).](localization/_static/lang.png)

3. <span data-ttu-id="dd679-536">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-536">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="dd679-537">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-537">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="dd679-538">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-538">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="dd679-539">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-539">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="dd679-540">인터넷 옵션</span><span class="sxs-lookup"><span data-stu-id="dd679-540">Internet Options</span></span>

<span data-ttu-id="dd679-541">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-541">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="dd679-542">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-542">Tap **Add a language**.</span></span> <span data-ttu-id="dd679-543">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-543">Add the language.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="dd679-544">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-544">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="dd679-545">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-545">Use a custom provider</span></span>

<span data-ttu-id="dd679-546">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-546">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="dd679-547">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-547">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="dd679-548">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-548">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="dd679-549">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-549">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="dd679-550">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-550">Set the culture programmatically</span></span> <span data-ttu-id="dd679-551">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-551">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="dd679-552">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-552">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="dd679-553">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-553">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="dd679-554">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-554">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="dd679-555">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-555">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="dd679-556">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-556">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="dd679-557">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="dd679-557">Model binding route data and query strings</span></span>

<span data-ttu-id="dd679-558">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-558">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="dd679-559">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="dd679-559">Globalization and localization terms</span></span> <span data-ttu-id="dd679-560">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-560">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="dd679-561">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-561">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="dd679-562">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-562">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="dd679-563">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-563">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="dd679-564">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-564">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="dd679-565">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-565">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="dd679-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="dd679-567">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-567">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="dd679-568">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-568">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="dd679-569">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-569">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="dd679-570">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-570">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="dd679-571">용어:</span><span class="sxs-lookup"><span data-stu-id="dd679-571">Terms:</span></span> <span data-ttu-id="dd679-572">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-572">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="dd679-573">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-573">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="dd679-574">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-574">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="dd679-575">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-575">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="dd679-576">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-576">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="dd679-577">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="dd679-577">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd679-578">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-578">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="dd679-579">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="dd679-579">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="dd679-580">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-580">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="dd679-581">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="dd679-581">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="dd679-582">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-582">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="dd679-583">추가 자료</span><span class="sxs-lookup"><span data-stu-id="dd679-583">Additional resources</span></span>

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="dd679-584">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-584">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="dd679-585">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-585">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="dd679-586">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="dd679-586">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="dd679-587">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="dd679-587">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="dd679-588">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="dd679-588">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="dd679-589">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="dd679-589">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="dd679-590">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-590">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="dd679-591">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-591">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="dd679-592">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-592">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="dd679-593">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-593">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="dd679-594">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-594">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="dd679-595">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-595">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="dd679-596">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-596">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="dd679-597">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-597">App localization involves the following:</span></span>

<span data-ttu-id="dd679-598">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-598">Make the app's content localizable</span></span> <span data-ttu-id="dd679-599">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-599">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-600">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-600">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-601">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd679-601">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="dd679-602">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="dd679-602">Make the app's content localizable</span></span> <span data-ttu-id="dd679-603"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 지역화된 앱을 개발할 때 생산성을 향상하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-603"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="dd679-604">`IStringLocalizer`는 <xref:System.Resources.ResourceManager> 및 <xref:System.Resources.ResourceReader>를 사용하여 런타임에 문화권 관련 리소스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-604">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="dd679-605">인터페이스에는 지역화된 문자열을 반환하기 위한 인덱서 및 `IEnumerable`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-605">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="dd679-606">`IStringLocalizer`는 리소스 파일에 기본 언어 문자열을 저장하도록 요구하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-606">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="dd679-607">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-607">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="dd679-608">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-608">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="dd679-609">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-609">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="dd679-610">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-610">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="dd679-611">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-611">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="dd679-612">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-612">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="dd679-613">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-613">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="dd679-614">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-614">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="dd679-615">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-615">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="dd679-616">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-616">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="dd679-617">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-617">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="dd679-618">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-618">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="dd679-619">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-619">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="dd679-620">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-620">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="dd679-621">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-621">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="dd679-622">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-622">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="dd679-623">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-623">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="dd679-624">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-624">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="dd679-625">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-625">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="dd679-626">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="dd679-626">View localization</span></span> <span data-ttu-id="dd679-627">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-627">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="dd679-628">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-628">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="dd679-629">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-629">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="dd679-630">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-630">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="dd679-631">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-631">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="dd679-632">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-632">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="dd679-633">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-633">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="dd679-634">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-634">Consider the following Razor markup:</span></span>

<span data-ttu-id="dd679-635">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-635">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="dd679-636">Key</span><span class="sxs-lookup"><span data-stu-id="dd679-636">Key</span></span>

<span data-ttu-id="dd679-637">값</span><span class="sxs-lookup"><span data-stu-id="dd679-637">Value</span></span> <span data-ttu-id="dd679-638">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-638">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="dd679-639">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-639">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="dd679-640">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-640">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="dd679-641">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="dd679-641">DataAnnotations localization</span></span> <span data-ttu-id="dd679-642">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-642">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="dd679-643">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-643">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="dd679-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="dd679-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="dd679-646">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-646">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="dd679-647">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="dd679-647">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="dd679-648">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-648">Using one resource string for multiple classes</span></span>

<span data-ttu-id="dd679-649">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-649">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="dd679-650">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-650">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="dd679-651">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-651">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="dd679-652">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="dd679-652">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="dd679-653">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="dd679-653">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="dd679-654">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-654">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="dd679-655">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-655">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="dd679-656">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-656">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="dd679-657">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-657">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="dd679-658">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-658">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="dd679-659">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-659">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="dd679-660">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-660">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="dd679-661">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-661">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="dd679-662">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-662">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="dd679-663">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="dd679-663">Resource files</span></span>

1. <span data-ttu-id="dd679-664">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-664">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.](localization/_static/newi.png)

2. <span data-ttu-id="dd679-667">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-667">"es" is the language code for Spanish.</span></span>

    ![Visual Studio에서 이 리소스 파일을 만들려면:](localization/_static/res.png)

3. <span data-ttu-id="dd679-669">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-669">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/hola.png)

    <span data-ttu-id="dd679-671">두 번째 바로 가기 메뉴는 강조 표시된 새 항목 명령을 보여 주는 추가에 대해 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-671">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="dd679-673">새 항목 추가 대화 상자</span><span class="sxs-lookup"><span data-stu-id="dd679-673">Add New Item dialog</span></span>

<span data-ttu-id="dd679-674">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-674">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="dd679-675">이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)</span><span class="sxs-lookup"><span data-stu-id="dd679-675">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="dd679-676">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-676">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="dd679-677">Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기</span><span class="sxs-lookup"><span data-stu-id="dd679-677">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="dd679-678">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="dd679-678">Resource file naming</span></span>

<span data-ttu-id="dd679-679">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-679">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="dd679-680">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-680">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="dd679-681">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-681">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="dd679-682">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-682">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="dd679-683">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-683">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="dd679-684">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-684">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="dd679-685">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-685">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="dd679-686">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-686">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="dd679-687">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-687">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="dd679-688">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-688">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="dd679-689">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-689">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="dd679-690">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="dd679-690">Resource name</span></span> |
|    |     |

<span data-ttu-id="dd679-691">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="dd679-691">Dot or path naming</span></span> <span data-ttu-id="dd679-692">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-692">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="dd679-693">점</span><span class="sxs-lookup"><span data-stu-id="dd679-693">Dot</span></span> <span data-ttu-id="dd679-694">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-694">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="dd679-695">경로</span><span class="sxs-lookup"><span data-stu-id="dd679-695">Path</span></span>

* <span data-ttu-id="dd679-696">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-696">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="dd679-697">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-697">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="dd679-698"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-698"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="dd679-699">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-699">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="dd679-700">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-700">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="dd679-701">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="dd679-701">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="dd679-702">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-702">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="dd679-703">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="dd679-703">RootNamespaceAttribute</span></span>
* <span data-ttu-id="dd679-704">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-704">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="dd679-705">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-705">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="dd679-706">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-706">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="dd679-707">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="dd679-707">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="dd679-708">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-708">Localization does not work by default.</span></span>

<span data-ttu-id="dd679-709">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-709">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="dd679-710">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-710">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="dd679-711">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="dd679-711">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="dd679-712">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-712">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="dd679-713">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="dd679-713">Culture fallback behavior</span></span> <span data-ttu-id="dd679-714">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-714">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="dd679-715">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-715">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="dd679-716">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-716">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="dd679-717">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-717">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="dd679-718">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-718">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="dd679-719">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-719">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="dd679-720">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-720">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="dd679-721">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-721">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="dd679-722">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-722">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="dd679-723">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="dd679-723">*Welcome.fr.resx*</span></span>

<span data-ttu-id="dd679-724">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="dd679-724">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="dd679-725">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-725">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="dd679-726">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-726">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="dd679-727">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-727">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="dd679-728">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="dd679-728">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="dd679-729">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-729">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="dd679-730">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-730">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="dd679-731">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-731">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="dd679-732">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="dd679-732">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="dd679-733">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-733">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="dd679-734">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="dd679-734">Add other cultures</span></span>

<span data-ttu-id="dd679-735">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-735">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="dd679-736">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="dd679-736">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="dd679-737">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-737">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="dd679-738">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="dd679-738">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="dd679-739">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="dd679-739">Configure localization</span></span> <span data-ttu-id="dd679-740">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-740">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="dd679-741">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-741">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="dd679-742">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-742">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="dd679-743">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-743">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="dd679-744">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-744">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="dd679-745">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-745">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="dd679-746">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-746">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="dd679-747">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="dd679-747">Localization middleware</span></span> <span data-ttu-id="dd679-748">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-748">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="dd679-749">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-749">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="dd679-750">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="dd679-750">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="dd679-751">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-751">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="dd679-752">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-752">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="dd679-753">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-753">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="dd679-754">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-754">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="dd679-755">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-755">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="dd679-756">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-756">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="dd679-757">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-757">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="dd679-758">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-758">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="dd679-759">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-759">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="dd679-760">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-760">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="dd679-761">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-761">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="dd679-762">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-762">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="dd679-763">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-763">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="dd679-764">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-764">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="dd679-765">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="dd679-765">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="dd679-766">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-766">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="dd679-767">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-767">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="dd679-768">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-768">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="dd679-769">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-769">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="dd679-770">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-770">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="dd679-771">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-771">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="dd679-772">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="dd679-772">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="dd679-773">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-773">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="dd679-774">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-774">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).](localization/_static/lang.png)

3. <span data-ttu-id="dd679-776">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-776">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="dd679-777">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-777">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="dd679-778">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-778">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="dd679-779">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-779">Tap **Languages**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="dd679-780">인터넷 옵션</span><span class="sxs-lookup"><span data-stu-id="dd679-780">Internet Options</span></span>

<span data-ttu-id="dd679-781">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-781">Tap **Set Language Preferences**.</span></span>

 - <span data-ttu-id="dd679-782">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-782">Tap **Add a language**.</span></span>
 - <span data-ttu-id="dd679-783">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-783">Add the language.</span></span>

<span data-ttu-id="dd679-784">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-784">Tap the language, then tap **Move Up**.</span></span>

<span data-ttu-id="dd679-785">콘텐츠-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="dd679-785">The Content-Language HTTP header</span></span>

<span data-ttu-id="dd679-786">[콘텐츠-언어](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) 엔터티 헤더:</span><span class="sxs-lookup"><span data-stu-id="dd679-786">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="dd679-787">대상 그룹을 위한 언어를 설명하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-787">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="dd679-788">사용자가 사용자 기본 설정 언어에 따라 구별할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-788">Allows a user to differentiate according to the users' own preferred language.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="dd679-789">엔터티 헤더는 HTTP 요청 및 응답에 모두 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-789">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="dd679-790">속성 `ApplyCurrentCultureToResponseHeaders`을(를) 설정하여 `Content-Language` 헤더를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-790">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span> <span data-ttu-id="dd679-791">`Content-Language` 헤더 추가:</span><span class="sxs-lookup"><span data-stu-id="dd679-791">Adding the `Content-Language` header:</span></span> <span data-ttu-id="dd679-792">RequestLocalizationMiddleware가 `CurrentUICulture`를 사용하여 `Content-Language` 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-792">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="dd679-793">응답 헤더 `Content-Language`를 명시적으로 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-793">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="dd679-794">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="dd679-794">Use a custom provider</span></span>

<span data-ttu-id="dd679-795">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-795">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="dd679-796">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-796">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="dd679-797">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-797">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="dd679-798">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-798">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="dd679-799">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="dd679-799">Set the culture programmatically</span></span> <span data-ttu-id="dd679-800">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-800">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="dd679-801">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-801">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="dd679-802">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-802">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="dd679-803">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-803">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="dd679-804">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-804">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="dd679-805">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-805">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="dd679-806">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="dd679-806">Model binding route data and query strings</span></span>

<span data-ttu-id="dd679-807">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-807">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="dd679-808">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="dd679-808">Globalization and localization terms</span></span> <span data-ttu-id="dd679-809">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-809">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="dd679-810">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-810">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="dd679-811">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-811">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="dd679-812">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-812">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="dd679-813">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-813">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="dd679-814">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-814">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="dd679-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="dd679-816">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dd679-816">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="dd679-817">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-817">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="dd679-818">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-818">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="dd679-819">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-819">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="dd679-820">용어:</span><span class="sxs-lookup"><span data-stu-id="dd679-820">Terms:</span></span> <span data-ttu-id="dd679-821">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-821">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="dd679-822">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-822">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="dd679-823">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-823">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="dd679-824">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-824">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="dd679-825">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-825">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="dd679-826">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="dd679-826">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd679-827">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-827">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="dd679-828">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="dd679-828">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="dd679-829">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-829">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="dd679-830">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="dd679-830">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="dd679-831">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="dd679-831">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="dd679-832">추가 자료</span><span class="sxs-lookup"><span data-stu-id="dd679-832">Additional resources</span></span>

::: moniker-end
