---
<span data-ttu-id="3a37b-101">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-102">'Blazor'</span></span>
- <span data-ttu-id="3a37b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-103">'Identity'</span></span>
- <span data-ttu-id="3a37b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-105">'Razor'</span></span>
- <span data-ttu-id="3a37b-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="3a37b-107">ASP.NET Core에서 세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3a37b-108">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3a37b-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3a37b-109">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3a37b-110">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3a37b-111">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3a37b-112">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3a37b-113">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3a37b-114">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3a37b-115">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3a37b-116">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-116">App localization involves the following:</span></span>

1. <span data-ttu-id="3a37b-117">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="3a37b-118">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3a37b-119">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3a37b-120">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a37b-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3a37b-121">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-121">Make the app's content localizable</span></span>

<span data-ttu-id="3a37b-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\`는 리소스 파일에 기본 언어 문자열을 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3a37b-123">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3a37b-124">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3a37b-125">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3a37b-126">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3a37b-127">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3a37b-128">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3a37b-129">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3a37b-130">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3a37b-131">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3a37b-132">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3a37b-133">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3a37b-134">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="3a37b-135">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-136">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3a37b-137">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3a37b-138">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3a37b-139">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="3a37b-140">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3a37b-141">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3a37b-142">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="3a37b-142">View localization</span></span>

<span data-ttu-id="3a37b-143">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3a37b-144">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3a37b-145">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3a37b-146">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3a37b-147">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3a37b-148">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3a37b-149">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3a37b-150">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3a37b-151">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3a37b-152">Key</span><span class="sxs-lookup"><span data-stu-id="3a37b-152">Key</span></span> | <span data-ttu-id="3a37b-153">값</span><span class="sxs-lookup"><span data-stu-id="3a37b-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="3a37b-154">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-155">'Blazor'</span></span>
- <span data-ttu-id="3a37b-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-156">'Identity'</span></span>
- <span data-ttu-id="3a37b-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-158">'Razor'</span></span>
- <span data-ttu-id="3a37b-159">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-159">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="3a37b-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="3a37b-161">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="3a37b-162">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-163">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3a37b-164">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-164">DataAnnotations localization</span></span>

<span data-ttu-id="3a37b-165">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3a37b-166">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3a37b-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3a37b-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3a37b-169">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3a37b-170">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3a37b-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3a37b-171">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3a37b-172">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="3a37b-173">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3a37b-174">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3a37b-175">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3a37b-176">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3a37b-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3a37b-177">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3a37b-178">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3a37b-179">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3a37b-180">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3a37b-181">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3a37b-182">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3a37b-183">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3a37b-184">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3a37b-185">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3a37b-186">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="3a37b-186">Resource files</span></span>

<span data-ttu-id="3a37b-187">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3a37b-188">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3a37b-189">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx*라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3a37b-190">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3a37b-191">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="3a37b-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3a37b-192">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

2. <span data-ttu-id="3a37b-195">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![새 항목 추가 대화 상자](localization/_static/res.png)

3. <span data-ttu-id="3a37b-197">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

    <span data-ttu-id="3a37b-199">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3a37b-201">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="3a37b-201">Resource file naming</span></span>

<span data-ttu-id="3a37b-202">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3a37b-203">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3a37b-204">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-205">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3a37b-206">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3a37b-207">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-208">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3a37b-209">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-210">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3a37b-211">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-212">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3a37b-213">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="3a37b-213">Resource name</span></span> | <span data-ttu-id="3a37b-214">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="3a37b-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="3a37b-215">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-216">'Blazor'</span></span>
- <span data-ttu-id="3a37b-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-217">'Identity'</span></span>
- <span data-ttu-id="3a37b-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-219">'Razor'</span></span>
- <span data-ttu-id="3a37b-220">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-221">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-222">'Blazor'</span></span>
- <span data-ttu-id="3a37b-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-223">'Identity'</span></span>
- <span data-ttu-id="3a37b-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-225">'Razor'</span></span>
- <span data-ttu-id="3a37b-226">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-227">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-228">'Blazor'</span></span>
- <span data-ttu-id="3a37b-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-229">'Identity'</span></span>
- <span data-ttu-id="3a37b-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-231">'Razor'</span></span>
- <span data-ttu-id="3a37b-232">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-233">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-234">'Blazor'</span></span>
- <span data-ttu-id="3a37b-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-235">'Identity'</span></span>
- <span data-ttu-id="3a37b-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-237">'Razor'</span></span>
- <span data-ttu-id="3a37b-238">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-238">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-240">'Blazor'</span></span>
- <span data-ttu-id="3a37b-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-241">'Identity'</span></span>
- <span data-ttu-id="3a37b-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-243">'Razor'</span></span>
- <span data-ttu-id="3a37b-244">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-245">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-246">'Blazor'</span></span>
- <span data-ttu-id="3a37b-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-247">'Identity'</span></span>
- <span data-ttu-id="3a37b-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-249">'Razor'</span></span>
- <span data-ttu-id="3a37b-250">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-251">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-252">'Blazor'</span></span>
- <span data-ttu-id="3a37b-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-253">'Identity'</span></span>
- <span data-ttu-id="3a37b-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-255">'Razor'</span></span>
- <span data-ttu-id="3a37b-256">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-257">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-258">'Blazor'</span></span>
- <span data-ttu-id="3a37b-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-259">'Identity'</span></span>
- <span data-ttu-id="3a37b-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-261">'Razor'</span></span>
- <span data-ttu-id="3a37b-262">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-262">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-263">------- | | Resources/Controllers.HomeController.fr.resx | 점  | | Resources/Controllers/HomeController.fr.resx  | 경로 | |    |     |</span><span class="sxs-lookup"><span data-stu-id="3a37b-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="3a37b-264">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3a37b-265">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="3a37b-266"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3a37b-267">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3a37b-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3a37b-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3a37b-270">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3a37b-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3a37b-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3a37b-272">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3a37b-273">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3a37b-274">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3a37b-275">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="3a37b-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3a37b-276">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-276">Localization does not work by default.</span></span>
* <span data-ttu-id="3a37b-277">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3a37b-278">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3a37b-279">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="3a37b-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3a37b-280">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3a37b-281">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="3a37b-281">Culture fallback behavior</span></span>

<span data-ttu-id="3a37b-282">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3a37b-283">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3a37b-284">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3a37b-285">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3a37b-286">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3a37b-287">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3a37b-288">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3a37b-289">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3a37b-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3a37b-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3a37b-292">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="3a37b-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3a37b-293">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3a37b-294">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3a37b-295">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3a37b-296">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="3a37b-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3a37b-297">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3a37b-298">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3a37b-299">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3a37b-300">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="3a37b-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3a37b-301">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3a37b-302">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="3a37b-302">Add other cultures</span></span>

<span data-ttu-id="3a37b-303">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3a37b-304">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="3a37b-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3a37b-305">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3a37b-306">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3a37b-307">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="3a37b-307">Configure localization</span></span>

<span data-ttu-id="3a37b-308">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3a37b-309">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="3a37b-310">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3a37b-311">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="3a37b-312">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3a37b-313">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3a37b-314">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3a37b-315">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3a37b-315">Localization middleware</span></span>

<span data-ttu-id="3a37b-316">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3a37b-317">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3a37b-318">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="3a37b-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3a37b-319">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3a37b-320">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3a37b-321">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3a37b-322">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3a37b-323">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3a37b-324">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3a37b-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-326">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="3a37b-327">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3a37b-328">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3a37b-329">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3a37b-330">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="3a37b-331">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3a37b-332">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3a37b-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-334">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3a37b-335">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3a37b-336">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3a37b-337">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3a37b-338">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="3a37b-339">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3a37b-340">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="3a37b-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3a37b-341">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3a37b-342">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3a37b-343">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="3a37b-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3a37b-344">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3a37b-345">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3a37b-346">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="3a37b-347">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-347">Tap **Languages**.</span></span>

    ![인터넷 옵션](localization/_static/lang.png)

3. <span data-ttu-id="3a37b-349">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="3a37b-350">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="3a37b-351">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-351">Add the language.</span></span>

6. <span data-ttu-id="3a37b-352">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="3a37b-353">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-353">Use a custom provider</span></span>

<span data-ttu-id="3a37b-354">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3a37b-355">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3a37b-356">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="3a37b-357">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3a37b-358">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-358">Set the culture programmatically</span></span>

<span data-ttu-id="3a37b-359">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3a37b-360">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3a37b-361">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3a37b-362">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3a37b-363">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3a37b-364">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3a37b-365">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="3a37b-365">Model binding route data and query strings</span></span>

<span data-ttu-id="3a37b-366">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3a37b-367">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="3a37b-367">Globalization and localization terms</span></span>

<span data-ttu-id="3a37b-368">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3a37b-369">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3a37b-370">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3a37b-371">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3a37b-372">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3a37b-373">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3a37b-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3a37b-375">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="3a37b-376">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3a37b-377">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3a37b-378">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3a37b-379">용어:</span><span class="sxs-lookup"><span data-stu-id="3a37b-379">Terms:</span></span>

* <span data-ttu-id="3a37b-380">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3a37b-381">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3a37b-382">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3a37b-383">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3a37b-384">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3a37b-385">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="3a37b-385">(for example "en", "es")</span></span>
* <span data-ttu-id="3a37b-386">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3a37b-387">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="3a37b-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3a37b-388">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3a37b-389">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="3a37b-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3a37b-390">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="3a37b-391">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3a37b-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3a37b-392">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3a37b-393">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3a37b-394">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="3a37b-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3a37b-395">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="3a37b-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3a37b-396">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="3a37b-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a37b-397">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3a37b-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3a37b-398">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3a37b-399">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3a37b-400">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3a37b-401">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3a37b-402">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3a37b-403">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3a37b-404">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3a37b-405">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-405">App localization involves the following:</span></span>

1. <span data-ttu-id="3a37b-406">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="3a37b-407">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3a37b-408">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3a37b-409">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a37b-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3a37b-410">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-410">Make the app's content localizable</span></span>

<span data-ttu-id="3a37b-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\`는 리소스 파일에 기본 언어 문자열을 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3a37b-412">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3a37b-413">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3a37b-414">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3a37b-415">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3a37b-416">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3a37b-417">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3a37b-418">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3a37b-419">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3a37b-420">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3a37b-421">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3a37b-422">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3a37b-423">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="3a37b-424">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-425">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3a37b-426">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3a37b-427">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3a37b-428">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="3a37b-429">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3a37b-430">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3a37b-431">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="3a37b-431">View localization</span></span>

<span data-ttu-id="3a37b-432">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3a37b-433">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3a37b-434">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3a37b-435">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3a37b-436">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3a37b-437">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3a37b-438">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3a37b-439">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3a37b-440">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3a37b-441">Key</span><span class="sxs-lookup"><span data-stu-id="3a37b-441">Key</span></span> | <span data-ttu-id="3a37b-442">값</span><span class="sxs-lookup"><span data-stu-id="3a37b-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="3a37b-443">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-444">'Blazor'</span></span>
- <span data-ttu-id="3a37b-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-445">'Identity'</span></span>
- <span data-ttu-id="3a37b-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-447">'Razor'</span></span>
- <span data-ttu-id="3a37b-448">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-448">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="3a37b-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="3a37b-450">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="3a37b-451">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-452">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3a37b-453">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-453">DataAnnotations localization</span></span>

<span data-ttu-id="3a37b-454">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3a37b-455">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3a37b-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3a37b-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3a37b-458">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3a37b-459">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3a37b-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3a37b-460">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3a37b-461">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="3a37b-462">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3a37b-463">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3a37b-464">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3a37b-465">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3a37b-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3a37b-466">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3a37b-467">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3a37b-468">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3a37b-469">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3a37b-470">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3a37b-471">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3a37b-472">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3a37b-473">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3a37b-474">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3a37b-475">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="3a37b-475">Resource files</span></span>

<span data-ttu-id="3a37b-476">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3a37b-477">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3a37b-478">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx*라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3a37b-479">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3a37b-480">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="3a37b-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3a37b-481">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

2. <span data-ttu-id="3a37b-484">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![새 항목 추가 대화 상자](localization/_static/res.png)

3. <span data-ttu-id="3a37b-486">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

    <span data-ttu-id="3a37b-488">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3a37b-490">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="3a37b-490">Resource file naming</span></span>

<span data-ttu-id="3a37b-491">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3a37b-492">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3a37b-493">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-494">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3a37b-495">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3a37b-496">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-497">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3a37b-498">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-499">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3a37b-500">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-501">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3a37b-502">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="3a37b-502">Resource name</span></span> | <span data-ttu-id="3a37b-503">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="3a37b-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="3a37b-504">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-505">'Blazor'</span></span>
- <span data-ttu-id="3a37b-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-506">'Identity'</span></span>
- <span data-ttu-id="3a37b-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-508">'Razor'</span></span>
- <span data-ttu-id="3a37b-509">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-510">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-511">'Blazor'</span></span>
- <span data-ttu-id="3a37b-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-512">'Identity'</span></span>
- <span data-ttu-id="3a37b-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-514">'Razor'</span></span>
- <span data-ttu-id="3a37b-515">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-516">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-517">'Blazor'</span></span>
- <span data-ttu-id="3a37b-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-518">'Identity'</span></span>
- <span data-ttu-id="3a37b-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-520">'Razor'</span></span>
- <span data-ttu-id="3a37b-521">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-522">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-523">'Blazor'</span></span>
- <span data-ttu-id="3a37b-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-524">'Identity'</span></span>
- <span data-ttu-id="3a37b-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-526">'Razor'</span></span>
- <span data-ttu-id="3a37b-527">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-527">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-529">'Blazor'</span></span>
- <span data-ttu-id="3a37b-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-530">'Identity'</span></span>
- <span data-ttu-id="3a37b-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-532">'Razor'</span></span>
- <span data-ttu-id="3a37b-533">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-534">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-535">'Blazor'</span></span>
- <span data-ttu-id="3a37b-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-536">'Identity'</span></span>
- <span data-ttu-id="3a37b-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-538">'Razor'</span></span>
- <span data-ttu-id="3a37b-539">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-540">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-541">'Blazor'</span></span>
- <span data-ttu-id="3a37b-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-542">'Identity'</span></span>
- <span data-ttu-id="3a37b-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-544">'Razor'</span></span>
- <span data-ttu-id="3a37b-545">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-546">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-547">'Blazor'</span></span>
- <span data-ttu-id="3a37b-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-548">'Identity'</span></span>
- <span data-ttu-id="3a37b-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-550">'Razor'</span></span>
- <span data-ttu-id="3a37b-551">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-551">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-552">------- | | Resources/Controllers.HomeController.fr.resx | 점  | | Resources/Controllers/HomeController.fr.resx  | 경로 | |    |     |</span><span class="sxs-lookup"><span data-stu-id="3a37b-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="3a37b-553">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3a37b-554">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="3a37b-555"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3a37b-556">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3a37b-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3a37b-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3a37b-559">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3a37b-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3a37b-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3a37b-561">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3a37b-562">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3a37b-563">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3a37b-564">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="3a37b-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3a37b-565">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-565">Localization does not work by default.</span></span>
* <span data-ttu-id="3a37b-566">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3a37b-567">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3a37b-568">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="3a37b-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3a37b-569">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3a37b-570">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="3a37b-570">Culture fallback behavior</span></span>

<span data-ttu-id="3a37b-571">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3a37b-572">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3a37b-573">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3a37b-574">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3a37b-575">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3a37b-576">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3a37b-577">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3a37b-578">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3a37b-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3a37b-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3a37b-581">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="3a37b-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3a37b-582">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3a37b-583">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3a37b-584">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3a37b-585">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="3a37b-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3a37b-586">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3a37b-587">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3a37b-588">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3a37b-589">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="3a37b-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3a37b-590">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3a37b-591">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="3a37b-591">Add other cultures</span></span>

<span data-ttu-id="3a37b-592">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3a37b-593">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="3a37b-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3a37b-594">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3a37b-595">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3a37b-596">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="3a37b-596">Configure localization</span></span>

<span data-ttu-id="3a37b-597">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3a37b-598">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="3a37b-599">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3a37b-600">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="3a37b-601">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3a37b-602">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3a37b-603">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3a37b-604">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3a37b-604">Localization middleware</span></span>

<span data-ttu-id="3a37b-605">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3a37b-606">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3a37b-607">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="3a37b-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3a37b-608">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3a37b-609">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3a37b-610">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3a37b-611">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3a37b-612">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3a37b-613">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3a37b-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-615">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="3a37b-616">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3a37b-617">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3a37b-618">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3a37b-619">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="3a37b-620">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3a37b-621">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3a37b-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-623">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3a37b-624">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3a37b-625">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3a37b-626">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3a37b-627">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="3a37b-628">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3a37b-629">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="3a37b-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3a37b-630">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3a37b-631">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3a37b-632">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="3a37b-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3a37b-633">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3a37b-634">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3a37b-635">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="3a37b-636">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-636">Tap **Languages**.</span></span>

    ![인터넷 옵션](localization/_static/lang.png)

3. <span data-ttu-id="3a37b-638">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="3a37b-639">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="3a37b-640">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-640">Add the language.</span></span>

6. <span data-ttu-id="3a37b-641">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="3a37b-642">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-642">Use a custom provider</span></span>

<span data-ttu-id="3a37b-643">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3a37b-644">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3a37b-645">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="3a37b-646">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3a37b-647">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-647">Set the culture programmatically</span></span>

<span data-ttu-id="3a37b-648">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3a37b-649">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3a37b-650">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3a37b-651">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3a37b-652">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3a37b-653">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3a37b-654">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="3a37b-654">Model binding route data and query strings</span></span>

<span data-ttu-id="3a37b-655">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3a37b-656">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="3a37b-656">Globalization and localization terms</span></span>

<span data-ttu-id="3a37b-657">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3a37b-658">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3a37b-659">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3a37b-660">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3a37b-661">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3a37b-662">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3a37b-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3a37b-664">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="3a37b-665">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3a37b-666">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3a37b-667">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3a37b-668">용어:</span><span class="sxs-lookup"><span data-stu-id="3a37b-668">Terms:</span></span>

* <span data-ttu-id="3a37b-669">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3a37b-670">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3a37b-671">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3a37b-672">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3a37b-673">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3a37b-674">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="3a37b-674">(for example "en", "es")</span></span>
* <span data-ttu-id="3a37b-675">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3a37b-676">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="3a37b-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3a37b-677">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3a37b-678">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="3a37b-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3a37b-679">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="3a37b-680">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3a37b-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3a37b-681">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3a37b-682">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3a37b-683">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="3a37b-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3a37b-684">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="3a37b-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3a37b-685">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="3a37b-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="3a37b-686">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/) 및 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="3a37b-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="3a37b-687">다국어 웹 사이트를 사용하면 사이트를 더 광범위한 대상에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="3a37b-688">ASP.NET Core는 다른 언어 및 문화권의 지역화를 위한 서비스 및 미들웨어를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="3a37b-689">국제화는 [전역화](/dotnet/api/system.globalization) 및 [지역화](/dotnet/standard/globalization-localization/localization)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="3a37b-690">세계화는 서로 다른 문화권을 지원하는 앱을 설계하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="3a37b-691">세계화는 특정 지역과 관련이 있는 정의된 언어 스크립트 집합의 입력, 표시 및 출력에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="3a37b-692">지역화는 이미 특정 문화권/로캘로 지역화 가능성을 위해 처리한 세계화된 앱을 조정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="3a37b-693">자세한 내용은 이 문서의 끝 부분에서 **세계화 및 지역화 용어**를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="3a37b-694">앱 지역화 과정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-694">App localization involves the following:</span></span>

1. <span data-ttu-id="3a37b-695">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="3a37b-696">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="3a37b-697">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="3a37b-698">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a37b-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="3a37b-699">앱의 콘텐츠를 지역화 가능하도록 만들기</span><span class="sxs-lookup"><span data-stu-id="3a37b-699">Make the app's content localizable</span></span>

<span data-ttu-id="3a37b-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\`는 리소스 파일에 기본 언어 문자열을 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="3a37b-701">지역화를 대상으로 하는 앱을 개발할 수 있으며 초기 개발에서 리소스 파일을 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="3a37b-702">아래 코드는 지역화에 대한 "About Title" 문자열을 래핑하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="3a37b-703">위의 코드에서 `IStringLocalizer<T>` 구현은 [종속성 주입](dependency-injection.md)에서 옵니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="3a37b-704">"About Title"의 지역화된 값을 찾을 수 없는 경우 인덱서 키가 반환됩니다. 즉, "About Title" 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="3a37b-705">앱에서 기본 언어 리터럴 문자열을 그대로 두고 앱 개발에 집중할 수 있도록 로컬라이저에서 래핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="3a37b-706">기본 언어로 앱을 개발하고 먼저 기본 리소스 파일을 만들지 않고 지역화 단계에 대한 준비를 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="3a37b-707">또는 기존의 접근 방식을 사용하고 기본 언어 문자열을 검색하도록 키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="3a37b-708">대부분의 개발자의 경우 기본 언어 *.resx* 파일을 갖지 않는 새 워크플로와 단순히 문자열 리터럴을 래핑하여 앱을 지역화하는 오버헤드를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="3a37b-709">다른 개발자는 더 긴 문자열 리터럴과 함께 작동하기 쉽고 지역화된 문자열을 쉽게 업데이트할 수 있으므로 기존의 작업 흐름을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="3a37b-710">HTML을 포함하는 리소스에 대해 `IHtmlLocalizer<T>` 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="3a37b-711">`IHtmlLocalizer` HTML은 리소스 문자열에 서식이 지정된 인수를 인코딩하지만 리소스 문자열 자체를 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="3a37b-712">아래 강조 표시된 샘플에서 `name` 매개 변수의 값만 HTML 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="3a37b-713">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-714">가장 낮은 수준에서 [종속성 주입](dependency-injection.md)의 `IStringLocalizerFactory`를 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="3a37b-715">위의 코드는 두 개의 각 팩터리 만들기 메서드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="3a37b-716">컨트롤러, 영역으로 지역화된 문자열을 분할하거나 하나의 컨테이너만을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="3a37b-717">샘플 앱에서 `SharedResource`라는 더미 클래스는 공유 리소스에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="3a37b-718">일부 개발자는 `Startup` 클래스를 사용하여 전역 또는 공유 문자열을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="3a37b-719">아래 샘플에서 `InfoController` 및 `SharedResource` 로컬라이저가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="3a37b-720">지역화 보기</span><span class="sxs-lookup"><span data-stu-id="3a37b-720">View localization</span></span>

<span data-ttu-id="3a37b-721">`IViewLocalizer` 서비스는 [보기](xref:mvc/views/overview)에 대한 지역화된 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="3a37b-722">`ViewLocalizer` 클래스는 이 인터페이스를 구현하고 보기 파일 경로에서 리소스 위치를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="3a37b-723">다음 코드는 `IViewLocalizer`의 기본 구현을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="3a37b-724">`IViewLocalizer`의 기본 구현은 보기의 파일 이름에 따라 리소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="3a37b-725">전역 공유 리소스 파일을 사용할 수 있는 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="3a37b-726">`ViewLocalizer`는 `IHtmlLocalizer`를 사용하여 로컬라이저를 구현하므로 Razor는 지역화된 문자열을 HTML 인코딩하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="3a37b-727">리소스 문자열을 매개 변수화할 수 있으며 `IViewLocalizer`는 리소스 문자열이 아닌 매개 변수를 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="3a37b-728">다음 Razor 태그를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="3a37b-729">프랑스어 리소스 파일은 다음을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="3a37b-730">Key</span><span class="sxs-lookup"><span data-stu-id="3a37b-730">Key</span></span> | <span data-ttu-id="3a37b-731">값</span><span class="sxs-lookup"><span data-stu-id="3a37b-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="3a37b-732">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-733">'Blazor'</span></span>
- <span data-ttu-id="3a37b-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-734">'Identity'</span></span>
- <span data-ttu-id="3a37b-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-736">'Razor'</span></span>
- <span data-ttu-id="3a37b-737">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-737">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="3a37b-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="3a37b-739">렌더링된 보기는 리소스 파일에서 HTML 표시를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="3a37b-740">**참고:** 일반적으로 HTML이 아닌 텍스트만 지역화하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="3a37b-741">보기에서 공유 리소스 파일을 사용하려면 `IHtmlLocalizer<T>`를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="3a37b-742">DataAnnotations 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-742">DataAnnotations localization</span></span>

<span data-ttu-id="3a37b-743">DataAnnotations 오류 메시지는 `IStringLocalizer<T>`로 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="3a37b-744">`ResourcesPath = "Resources"` 옵션을 사용하여 `RegisterViewModel`의 오류 메시지는 다음 경로 중 하나에 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="3a37b-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="3a37b-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="3a37b-747">ASP.NET Core MVC 1.1.0 이상에서 비-유효성 검사 특성이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="3a37b-748">ASP.NET Core MVC 1.0은 비-유효성 검사 특성에 대한 지역화된 문자열을 조회하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3a37b-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="3a37b-749">다중 클래스에 대해 하나의 리소스 문자열 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="3a37b-750">다음 코드는 다중 클래스를 사용하여 유효성 검사 특성에 대해 하나의 리소스 문자열을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="3a37b-751">위의 코드에서 `SharedResource`는 유효성 검사 메시지가 저장되는 resx에 해당하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="3a37b-752">이 접근 방식으로 DataAnnotations는 각 클래스에 대한 리소스 대신 `SharedResource`만을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="3a37b-753">지원하는 언어 및 문화권에 대한 지역화된 리소스 제공</span><span class="sxs-lookup"><span data-stu-id="3a37b-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="3a37b-754">SupportedCultures 및 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="3a37b-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="3a37b-755">ASP.NET Core를 사용하면 두 문화권 값 `SupportedCultures` 및 `SupportedUICultures`를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="3a37b-756">`SupportedCultures`에 대한 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 개체는 날짜, 시간, 숫자 및 통화 형식과 같은 문화권 종속 함수의 결과를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="3a37b-757">`SupportedCultures`는 또한 텍스트, 대/소문자 규칙 및 문자열 비교의 정렬 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="3a37b-758">서버가 문화권을 가져오는 방법에 대한 자세한 내용은 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="3a37b-759">`SupportedUICultures`는 [ResourceManager](/dotnet/api/system.resources.resourcemanager)에서 조회하는 번역된 문자열( *.resx* 파일에서)을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="3a37b-760">`ResourceManager`는 `CurrentUICulture`에서 결정되는 문화권별 문자열을 단순히 조회합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="3a37b-761">.NET의 모든 스레드에는 `CurrentCulture` 및 `CurrentUICulture` 개체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="3a37b-762">ASP.NET Core는 문화권 종속 기능을 렌더링할 때 이러한 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="3a37b-763">예를 들어 현재 스레드의 문화권이 "en-US"(영어, 미국)로 설정되어 있으면 `DateTime.Now.ToLongDateString()`은 "Thursday, February 18, 2016"을 표시하지만 `CurrentCulture`가 "es-ES"(스페인어, 스페인)로 설정되어 있으면 출력은 "jueves, 18 de febrero de 2016"이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="3a37b-764">리소스 파일</span><span class="sxs-lookup"><span data-stu-id="3a37b-764">Resource files</span></span>

<span data-ttu-id="3a37b-765">리소스 파일은 코드에서 지역화 가능한 문자열을 구분하는 데 유용한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="3a37b-766">기본이 아닌 언어에 대한 번역된 문자열은 *.resx* 리소스 파일에서 격리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="3a37b-767">예를 들어 번역된 문자열을 포함하는 *Welcome.es.resx*라는 스페인어 리소스 파일을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="3a37b-768">"es"는 스페인어 언어 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="3a37b-769">Visual Studio에서 이 리소스 파일을 만들려면:</span><span class="sxs-lookup"><span data-stu-id="3a37b-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="3a37b-770">**솔루션 탐색기**에서 리소스 파일을 포함하는 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![중첩된 바로 가기 메뉴: 솔루션 탐색기에서 바로 가기 메뉴가 리소스에 대해 열려 있습니다.](localization/_static/newi.png)

2. <span data-ttu-id="3a37b-773">**설치된 템플릿 검색** 상자에 "리소스"를 입력하고 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![새 항목 추가 대화 상자](localization/_static/res.png)

3. <span data-ttu-id="3a37b-775">**이름** 열에 키 값(네이티브 문자열)을 입력하고 **값** 열에 번역된 문자열을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![이름 열에 Hello라는 단어가 있고 값 열에 Hola라는 단어(스페인어로 Hello)가 있는 Welcome.es.resx 파일(스페인어에 대한 Welcome 리소스 파일)](localization/_static/hola.png)

    <span data-ttu-id="3a37b-777">Visual Studio는 *Welcome.es.resx* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Welcome Spanish(es) 리소스 파일을 나타내는 솔루션 탐색기](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="3a37b-779">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="3a37b-779">Resource file naming</span></span>

<span data-ttu-id="3a37b-780">리소스의 이름은 해당 클래스의 전체 형식 이름에서 어셈블리 이름을 빼서 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="3a37b-781">예를 들어 주 어셈블리가 `LocalizationWebsite.Web.Startup` 클래스에 대해 `LocalizationWebsite.Web.dll`인 프로젝트에서 프랑스어 리소스는 *Startup.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="3a37b-782">`LocalizationWebsite.Web.Controllers.HomeController` 클래스에 대한 리소스는 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-783">대상 클래스의 네임스페이스가 어셈블리 이름과 동일하지 않은 경우 전체 형식 이름이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="3a37b-784">예를 들어 샘플 프로젝트에서 `ExtraNamespace.Tools` 형식에 대한 리소스는 *ExtraNamespace.Tools.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="3a37b-785">샘플 프로젝트에서 `ConfigureServices` 메서드는 `ResourcesPath`를 "리소스"로 설정하므로 홈 컨트롤러의 프랑스어 리소스 파일에 대한 프로젝트 상대 경로는 *Resources/Controllers.HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-786">또는 폴더를 사용하여 리소스 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="3a37b-787">홈 컨트롤러의 경우 경로는 *Resources/Controllers/HomeController.fr.resx*입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-788">`ResourcesPath` 옵션을 사용하지 않는 경우 *.resx* 파일은 프로젝트 기본 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="3a37b-789">`HomeController`에 대한 리소스 파일은 *Controllers.HomeController.fr.resx*로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="3a37b-790">점 또는 경로 명명 규칙을 사용하도록 선택하는 것은 리소스 파일을 구성하려는 방법에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="3a37b-791">리소스 이름</span><span class="sxs-lookup"><span data-stu-id="3a37b-791">Resource name</span></span> | <span data-ttu-id="3a37b-792">점 또는 경로 명명</span><span class="sxs-lookup"><span data-stu-id="3a37b-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="3a37b-793">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-794">'Blazor'</span></span>
- <span data-ttu-id="3a37b-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-795">'Identity'</span></span>
- <span data-ttu-id="3a37b-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-797">'Razor'</span></span>
- <span data-ttu-id="3a37b-798">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-799">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-800">'Blazor'</span></span>
- <span data-ttu-id="3a37b-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-801">'Identity'</span></span>
- <span data-ttu-id="3a37b-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-803">'Razor'</span></span>
- <span data-ttu-id="3a37b-804">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-805">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-806">'Blazor'</span></span>
- <span data-ttu-id="3a37b-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-807">'Identity'</span></span>
- <span data-ttu-id="3a37b-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-809">'Razor'</span></span>
- <span data-ttu-id="3a37b-810">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-811">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-812">'Blazor'</span></span>
- <span data-ttu-id="3a37b-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-813">'Identity'</span></span>
- <span data-ttu-id="3a37b-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-815">'Razor'</span></span>
- <span data-ttu-id="3a37b-816">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-816">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-818">'Blazor'</span></span>
- <span data-ttu-id="3a37b-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-819">'Identity'</span></span>
- <span data-ttu-id="3a37b-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-821">'Razor'</span></span>
- <span data-ttu-id="3a37b-822">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-823">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-824">'Blazor'</span></span>
- <span data-ttu-id="3a37b-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-825">'Identity'</span></span>
- <span data-ttu-id="3a37b-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-827">'Razor'</span></span>
- <span data-ttu-id="3a37b-828">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-829">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-830">'Blazor'</span></span>
- <span data-ttu-id="3a37b-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-831">'Identity'</span></span>
- <span data-ttu-id="3a37b-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-833">'Razor'</span></span>
- <span data-ttu-id="3a37b-834">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3a37b-835">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3a37b-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="3a37b-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-836">'Blazor'</span></span>
- <span data-ttu-id="3a37b-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3a37b-837">'Identity'</span></span>
- <span data-ttu-id="3a37b-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3a37b-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="3a37b-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3a37b-839">'Razor'</span></span>
- <span data-ttu-id="3a37b-840">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3a37b-840">'SignalR' uid:</span></span> 

<span data-ttu-id="3a37b-841">------- | | Resources/Controllers.HomeController.fr.resx | 점  | | Resources/Controllers/HomeController.fr.resx  | 경로 | |    |     |</span><span class="sxs-lookup"><span data-stu-id="3a37b-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="3a37b-842">Razor 뷰에서 `@inject IViewLocalizer`를 사용하는 리소스 파일은 유사한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="3a37b-843">보기에 대한 리소스 파일은 점 이름 지정 또는 경로 이름 지정을 사용하여 이름이 지정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="3a37b-844"> 뷰 리소스 파일은 연결된 보기 파일의 경로를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="3a37b-845">`ResourcesPath`를 "리소스"로 설정했다고 가정하면, *Views/Home/About.cshtml* 보기와 연결된 프랑스어 리소스 파일은 다음 중 하나가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="3a37b-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="3a37b-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="3a37b-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="3a37b-848">`ResourcesPath` 옵션을 사용하지 않는 경우 보기에 대한 *.resx* 파일은 보기와 동일한 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="3a37b-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="3a37b-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="3a37b-850">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 속성은 어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우 어셈블리의 루트 네임 스페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="3a37b-851">이 오류는 프로젝트 이름이 유효한 .NET 식별자가 아닌 경우 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="3a37b-852">예를 들어 `my-project-name.csproj`가 루트 네임스페이스 `my_project_name`과 어셈블리 이름 `my-project-name`을 사용하면 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="3a37b-853">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다른 경우:</span><span class="sxs-lookup"><span data-stu-id="3a37b-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="3a37b-854">지역화는 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-854">Localization does not work by default.</span></span>
* <span data-ttu-id="3a37b-855">지역화는 리소스가 어셈블리 내에서 검색되는 방식으로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="3a37b-856">`RootNamespace`는 실행 중인 프로세스에 사용할 수 없는 빌드 시간 값입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="3a37b-857">`RootNamespace`가 `AssemblyName`과 다른 경우, 다음을 *AssemblyInfo.cs*에 포함합니다(매개 변수 값을 실제 값으로 대체하여 사용).</span><span class="sxs-lookup"><span data-stu-id="3a37b-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="3a37b-858">이전 코드를 사용하면 resx 파일을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="3a37b-859">문화권 대체 동작</span><span class="sxs-lookup"><span data-stu-id="3a37b-859">Culture fallback behavior</span></span>

<span data-ttu-id="3a37b-860">리소스를 검색할 때 지역화는 "문화권 대체"에 참여합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="3a37b-861">요청된 문화권에서 시작하여 찾을 수 없으면, 해당 문화권의 부모 문화권으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="3a37b-862">그 밖에도 [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 속성은 부모 문화권을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="3a37b-863">이는 일반적으로(항상 그렇지는 않음) ISO에서 국가 기호를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="3a37b-864">예를 들어 멕시코에서 사용되는 스페인어는 "es-MX"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="3a37b-865">이 문화권의 부모는 "es"로, 특정 국가에 국한되지 않는 스페인어를 말합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="3a37b-866">사이트가 문화권 "fr-CA"를 사용하여 "시작" 리소스에 대한 요청을 수신한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="3a37b-867">지역화 시스템은 다음 리소스를 순서대로 찾고, 첫 번째 일치 항목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="3a37b-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="3a37b-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="3a37b-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="3a37b-870">*Welcome.resx*(`NeutralResourcesLanguage`가 "fr-CA"인 경우)</span><span class="sxs-lookup"><span data-stu-id="3a37b-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="3a37b-871">예를 들어 ".fr" 문화권 지정자를 제거하고 프랑스어로 설정된 문화권이 있는 경우 기본 리소스 파일이 읽혀지고 문자열이 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="3a37b-872">리소스 관리자는 요청된 문화권에 맞지 않는 경우에 대한 기본 또는 대체 리소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="3a37b-873">요청된 문화권에 대한 리소스가 없을 때 키를 반환하려는 경우 기본 리소스 파일이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="3a37b-874">Visual Studio를 사용하여 리소스 파일 생성</span><span class="sxs-lookup"><span data-stu-id="3a37b-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="3a37b-875">파일 이름에 문화권이 없이(예: *Welcome.resx*) Visual Studio에서 리소스 파일을 만드는 경우 Visual Studio는 각 문자열에 대한 속성이 있는 C# 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="3a37b-876">일반적으로 이는 사용자가 ASP.NET Core에서 원하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="3a37b-877">일반적으로 기본 *.resx* 리소스 파일(문화권 이름이 없는 *.resx* 파일)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="3a37b-878">문화권 이름으로 *.resx* 파일을 만드는 것이 좋습니다(예: *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="3a37b-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="3a37b-879">문화권 이름으로 *.resx* 파일을 만드는 경우 Visual Studio는 클래스 파일을 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="3a37b-880">다른 문화권 추가</span><span class="sxs-lookup"><span data-stu-id="3a37b-880">Add other cultures</span></span>

<span data-ttu-id="3a37b-881">각 언어 및 문화권 조합(기본 언어 이외)에는 고유한 리소스 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="3a37b-882">ISO 언어 코드가 파일 이름의 일부인 새 리소스 파일을 만들어 서로 다른 문화권 및 로캘에 대한 리소스 파일을 만듭니다(예: **en-us**, **fr-ca** 및 **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="3a37b-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="3a37b-883">이러한 ISO 코드는 *Welcome.es-MX.resx*(스페인어/멕시코)처럼 파일 이름과 *.resx* 파일 확장명 사이에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="3a37b-884">각 요청에 대한 언어/문화권을 선택하는 전략 구현</span><span class="sxs-lookup"><span data-stu-id="3a37b-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="3a37b-885">지역화 구성</span><span class="sxs-lookup"><span data-stu-id="3a37b-885">Configure localization</span></span>

<span data-ttu-id="3a37b-886">지역화는 `Startup.ConfigureServices` 메서드에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="3a37b-887">`AddLocalization`은 서비스 컨테이너에 지역화 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="3a37b-888">위의 코드는 또한 "리소스"에 대한 리소스 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="3a37b-889">`AddViewLocalization`은 지역화된 보기 파일에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="3a37b-890">이 샘플 보기에서 지역화는 보기 파일 접미사를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="3a37b-891">예를 들어 *Index.fr.cshtml* 파일에서 "fr"입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="3a37b-892">`AddDataAnnotationsLocalization`은 `IStringLocalizer` 추상화를 통해 지역화된 `DataAnnotations` 유효성 검사 메시지에 대한 지원을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="3a37b-893">지역화 미들웨어</span><span class="sxs-lookup"><span data-stu-id="3a37b-893">Localization middleware</span></span>

<span data-ttu-id="3a37b-894">요청에서 현재 문화권은 지역화 [미들웨어](xref:fundamentals/middleware/index)에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="3a37b-895">지역화 미들웨어는 `Startup.Configure` 메서드에서 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="3a37b-896">지역화 미들웨어는 요청 문화권을 확인할 수 있는 모든 미들웨어 전에 구성되어야 합니다(예: `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="3a37b-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3a37b-897">`UseRequestLocalization`은 `RequestLocalizationOptions` 개체를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="3a37b-898">모든 요청의 `RequestLocalizationOptions`에서 `RequestCultureProvider`의 목록이 열거되고 요청 문화권을 성공적으로 결정할 수 있는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="3a37b-899">기본 공급자는 `RequestLocalizationOptions` 클래스에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="3a37b-900">기본 목록은 가장 구체적인 것에서 덜 구체적으로 것으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="3a37b-901">문서의 뒷부분에서 순서를 변경하고 사용자 지정 문화권 공급자를 추가하는 방법을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="3a37b-902">공급자가 요청 문화권을 확인할 수 없는 경우 `DefaultRequestCulture`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="3a37b-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-904">일부 앱은 쿼리 문자열을 사용하여 [문화권 및 UI 문화권](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="3a37b-905">쿠키 또는 수용-언어 헤더 방식을 사용하는 앱의 경우 URL에 쿼리 문자열을 추가하는 것은 코드 디버깅 및 테스트에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="3a37b-906">기본적으로 `QueryStringRequestCultureProvider`는 `RequestCultureProvider` 목록에서 첫 번째 지역화 공급자로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="3a37b-907">`culture` 및 `ui-culture`에 쿼리 문자열 매개 변수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="3a37b-908">다음 예제는 특정 문화권(언어 및 지역)을 스페인어/멕시코로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="3a37b-909">둘 중 하나만을 전달하는 경우(`culture` 또는 `ui-culture`) 쿼리 문자열 공급자는 전달한 것을 사용하여 두 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="3a37b-910">예를 들어 문화권만을 설정하면 `Culture` 및 `UICulture` 모두를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="3a37b-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="3a37b-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="3a37b-912">프로덕션 앱은 종종 메커니즘을 제공하여 ASP.NET Core 문화권 쿠키로 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="3a37b-913">`MakeCookieValue` 메서드를 사용하여 쿠키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="3a37b-914">`CookieRequestCultureProvider` `DefaultCookieName`은 사용자의 기본 문화권 정보를 추적하는 데 사용되는 기본 쿠키 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="3a37b-915">기본 쿠키 이름은 `.AspNetCore.Culture`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="3a37b-916">쿠키 형식은 `c=%LANGCODE%|uic=%LANGCODE%`이며 여기서 `c`는 `Culture`이며 `uic`는 `UICulture`입니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="3a37b-917">문화권 정보 및 UI 문화권 중 하나만 지정하는 경우 지정된 문화권은 문화권 정보 및 UI 문화권 모두에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="3a37b-918">수용-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="3a37b-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="3a37b-919">[수용-언어 헤더](https://www.w3.org/International/questions/qa-accept-lang-locales)는 대부분의 브라우저에서 설정할 수 있으며 원래 사용자의 언어를 지정하도록 계획되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="3a37b-920">이 설정은 브라우저가 전송하도록 설정된 것 또는 기본 운영 체제에서 상속한 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="3a37b-921">브라우저 요청에서 수용-언어 HTTP 헤더는 사용자의 기본 언어를 검색하는 확실한 방법이 아닙니다([브라우저에서 언어 기본 설정 설정](https://www.w3.org/International/questions/qa-lang-priorities.en.php) 참조).</span><span class="sxs-lookup"><span data-stu-id="3a37b-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="3a37b-922">프로덕션 앱은 사용자가 선택한 문화권을 사용자 지정하는 방법을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="3a37b-923">IE에서 수용-언어 HTTP 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="3a37b-924">기어 아이콘에서 **인터넷 옵션**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="3a37b-925">**언어**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-925">Tap **Languages**.</span></span>

    ![인터넷 옵션](localization/_static/lang.png)

3. <span data-ttu-id="3a37b-927">**언어 기본 설정 설정**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="3a37b-928">**언어 추가**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="3a37b-929">언어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-929">Add the language.</span></span>

6. <span data-ttu-id="3a37b-930">언어를 누른 다음, **위로 이동**을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="3a37b-931">콘텐츠-언어 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="3a37b-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="3a37b-932">[콘텐츠-언어](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) 엔터티 헤더:</span><span class="sxs-lookup"><span data-stu-id="3a37b-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="3a37b-933">대상 그룹을 위한 언어를 설명하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="3a37b-934">사용자가 사용자 기본 설정 언어에 따라 구별할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="3a37b-935">엔터티 헤더는 HTTP 요청 및 응답에 모두 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="3a37b-936">속성 `ApplyCurrentCultureToResponseHeaders`을(를) 설정하여 `Content-Language` 헤더를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="3a37b-937">`Content-Language` 헤더 추가:</span><span class="sxs-lookup"><span data-stu-id="3a37b-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="3a37b-938">RequestLocalizationMiddleware가 `CurrentUICulture`를 사용하여 `Content-Language` 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="3a37b-939">응답 헤더 `Content-Language`를 명시적으로 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="3a37b-940">사용자 지정 공급자 사용</span><span class="sxs-lookup"><span data-stu-id="3a37b-940">Use a custom provider</span></span>

<span data-ttu-id="3a37b-941">소비자가 자신의 언어 및 문화권을 데이터베이스에 저장하도록 하기를 원한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="3a37b-942">공급자를 작성하여 사용자에 대한 이러한 값을 조회할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="3a37b-943">다음 코드에서는 사용자 지정 공급자를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="3a37b-944">`RequestLocalizationOptions`를 사용하여 지역화 공급자를 추가하거나 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="3a37b-945">프로그래밍 방식으로 문화권 설정</span><span class="sxs-lookup"><span data-stu-id="3a37b-945">Set the culture programmatically</span></span>

<span data-ttu-id="3a37b-946">[GitHub](https://github.com/aspnet/entropy)에서 이 샘플 **Localization.StarterWeb** 프로젝트는 `Culture`를 설정하는 UI를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="3a37b-947">*Views/Shared/_SelectLanguagePartial.cshtml* 파일을 통해 지원되는 문화권의 목록에서 문화권을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="3a37b-948">*Views/Shared/_SelectLanguagePartial.cshtml* 파일은 레이아웃 파일의 `footer` 섹션에 추가되므로 모든 보기에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="3a37b-949">`SetLanguage` 메서드는 문화권 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="3a37b-950">*_SelectLanguagePartial.cshtml*을 이 프로젝트에 대한 샘플 코드에 플러그 인할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="3a37b-951">[GitHub](https://github.com/aspnet/entropy)의 **Localization.StarterWeb** 프로젝트에는 [종속성 주입](dependency-injection.md) 컨테이너를 통해 Razor 부분에 `RequestLocalizationOptions`를 흐르도록 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="3a37b-952">모델 바인딩 경로 데이터 및 쿼리 문자열</span><span class="sxs-lookup"><span data-stu-id="3a37b-952">Model binding route data and query strings</span></span>

<span data-ttu-id="3a37b-953">[모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작](xref:mvc/models/model-binding#glob)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="3a37b-954">세계화 및 지역화 용어</span><span class="sxs-lookup"><span data-stu-id="3a37b-954">Globalization and localization terms</span></span>

<span data-ttu-id="3a37b-955">또한 앱을 지역화하는 프로세스에는 최신 소프트웨어 개발에 일반적으로 사용되는 관련 문자 집합에 대한 기본적인 이해 및 관련된 문제에 대한 이해가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="3a37b-956">모든 컴퓨터가 텍스트를 숫자(코드)로 저장하지만 다른 시스템은 다른 숫자를 사용하여 동일한 텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="3a37b-957">지역화 프로세스는 특정 문화권/로캘에 대한 앱 UI(사용자 인터페이스) 번역을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="3a37b-958">[지역화 가능성](/dotnet/standard/globalization-localization/localizability-review)은 세계화된 앱이 지역화에 대해 준비가 되어 있는지 확인하기 위한 중간 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="3a37b-959">문화권 이름에 대한 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 형식은 `<languagecode2>-<country/regioncode2>`이며, 여기서 `<languagecode2>`는 언어 코드이며 `<country/regioncode2>`는 하위 문화권 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="3a37b-960">예를 들어 스페인어(칠레)의 경우 `es-CL`, 영어(미국)의 경우 `en-US` 및 영어(오스트레일리아)의 경우 `en-AU`입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="3a37b-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)은 언어와 관련된 ISO 639 두 문자의 소문자 문화권 코드와 국가 또는 지역과 관련된 ISO 3166 두 문자의 대문자 하위 문화권 코드의 조합입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="3a37b-962">[언어 문화권 이름](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a37b-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="3a37b-963">국제화는 종종 "I18N"으로 단축됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="3a37b-964">약어는 첫 번째 및 마지막 문자와 둘 사이의 문자 수를 사용하므로 18은 첫 번째 "I"와 마지막 "N" 사이의 문자 수를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="3a37b-965">세계화(G11N) 및 지역화(L10N)에도 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="3a37b-966">용어:</span><span class="sxs-lookup"><span data-stu-id="3a37b-966">Terms:</span></span>

* <span data-ttu-id="3a37b-967">세계화(G11N): 앱이 다른 언어 및 지역을 지원하도록 만드는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="3a37b-968">지역화(L10N): 지정된 언어 및 지역에 대해 앱을 사용자 지정하는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="3a37b-969">국제화(I18N): 세계화와 지역화를 모두 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="3a37b-970">문화권: 언어이며 경우에 따라 지역입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="3a37b-971">중립 문화권: 지정한 언어가 있지만 지정된 지역이 없는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="3a37b-972">(예: "en", "es")</span><span class="sxs-lookup"><span data-stu-id="3a37b-972">(for example "en", "es")</span></span>
* <span data-ttu-id="3a37b-973">특정 문화권: 지정된 언어 및 지역이 있는 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="3a37b-974">(예: "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="3a37b-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="3a37b-975">부모 문화권: 특정 문화권을 포함하는 중립 문화권입니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="3a37b-976">(예: "en"은 "en-US" 및 "en-GB"의 부모 문화권)</span><span class="sxs-lookup"><span data-stu-id="3a37b-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="3a37b-977">로캘: 로캘은 문화권과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="3a37b-978">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3a37b-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="3a37b-979">[Localization.StarterWeb 프로젝트](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)는 문서에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a37b-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="3a37b-980">.NET 애플리케이션 전역화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="3a37b-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="3a37b-981">.resx 파일의 리소스</span><span class="sxs-lookup"><span data-stu-id="3a37b-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="3a37b-982">Microsoft 다국어 앱 도구 키트</span><span class="sxs-lookup"><span data-stu-id="3a37b-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="3a37b-983">지역화 및 제네릭</span><span class="sxs-lookup"><span data-stu-id="3a37b-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
