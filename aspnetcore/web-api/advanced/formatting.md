---
<span data-ttu-id="228d9-101">제목: ASP.NET Core Web API author에서 응답 데이터 서식 지정: ardalis description: ASP.NET Core Web API에서 응답 데이터의 형식을 지정 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="228d9-102">H1Hack27Feb2017: riande ms. custom: ms. date: 04/17/2020 no loc:</span><span class="sxs-lookup"><span data-stu-id="228d9-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="228d9-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="228d9-103">'Blazor'</span></span>
- <span data-ttu-id="228d9-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="228d9-104">'Identity'</span></span>
- <span data-ttu-id="228d9-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="228d9-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="228d9-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="228d9-106">'Razor'</span></span>
- <span data-ttu-id="228d9-107">' SignalR ' uid: web-api/고급/서식</span><span class="sxs-lookup"><span data-stu-id="228d9-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="228d9-108">ASP.NET Core Web API에서 응답 데이터 서식 지정</span><span class="sxs-lookup"><span data-stu-id="228d9-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="228d9-109">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="228d9-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="228d9-110">ASP.NET Core MVC는 응답 데이터 서식 지정을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="228d9-111">응답 데이터는 특정 형식 또는 클라이언트 요청 형식에 대한 응답으로 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="228d9-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="228d9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="228d9-113">형식별 작업 결과</span><span class="sxs-lookup"><span data-stu-id="228d9-113">Format-specific Action Results</span></span>

<span data-ttu-id="228d9-114">일부 작업 결과 형식은 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 및 <xref:Microsoft.AspNetCore.Mvc.ContentResult>와 같이 특정 형식과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="228d9-115">작업은 클라이언트 기본 설정에 관계 없이 특정 형식으로 서식이 지정된 결과를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="228d9-116">예를 들어 `JsonResult`를 반환하면 JSON 형식의 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="228d9-117">`ContentResult` 또는 문자열 하나를 반환하면 일반 텍스트 형식의 문자열 데이터가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="228d9-118">특정 형식을 반환하는 작업은 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="228d9-119">ASP.NET Core는 모든 개체 반환 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="228d9-120"><xref:Microsoft.AspNetCore.Mvc.IActionResult> 형식이 아닌 개체를 반환하는 작업의 결과는 적절한 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 구현을 사용하여 직렬화됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="228d9-121">자세한 내용은 <xref:web-api/action-return-types>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="228d9-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="228d9-122">기본 제공 도우미 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>는 다음 JSON 형식 데이터를 반환합니다. [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="228d9-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="228d9-123">샘플 다운로드는 작성자 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="228d9-124">F12 브라우저 개발자 도구 또는 이전 코드의 [Postman](https://www.getpostman.com/tools) 사용:</span><span class="sxs-lookup"><span data-stu-id="228d9-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="228d9-125">\*\*content-type:이 포함된 응답 헤더 \*\* `application/json; charset=utf-8`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="228d9-126">요청 헤더가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-126">The request headers are displayed.</span></span> <span data-ttu-id="228d9-127">예를 들어 `Accept` 헤더가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-127">For example, the `Accept` header.</span></span> <span data-ttu-id="228d9-128">`Accept` 헤더는 앞의 코드에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="228d9-129">서식 있는 일반 텍스트 데이터를 반환하려면 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 및 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="228d9-130">위의 코드에서 반환된 `Content-Type`은 `text/plain`입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="228d9-131">문자열을 반환하면 `text/plain`의 `Content-Type`이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="228d9-132">반환 형식이 여러 개인 작업의 경우 `IActionResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="228d9-133">예를 들어 수행된 작업의 결과에 따라 서로 다른 HTTP 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="228d9-134">콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="228d9-134">Content negotiation</span></span>

<span data-ttu-id="228d9-135">콘텐츠 협상은 클라이언트가 [헤더 수락](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)을 지정하는 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="228d9-136">ASP.NET Core MVC에서 사용하는 기본 형식은 [JSON](https://json.org/)입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="228d9-137">콘텐츠 협상은 다음과 같은 특징을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-137">Content negotiation is:</span></span>

* <span data-ttu-id="228d9-138"><xref:Microsoft.AspNetCore.Mvc.ObjectResult>에 의해 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="228d9-139">도우미 메서드에서 반환된 상태 코드별 작업 결과 상태 코드에 기본적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="228d9-140">작업 결과 도우미 메서드는 `ObjectResult`를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="228d9-141">모델 형식이 반환되는 경우 반환 형식은 `ObjectResult`입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="228d9-142">다음 작업 메서드는 `Ok` 및 `NotFound` 도우미 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="228d9-143">기본적으로 ASP.NET Core는 `application/json`, `text/json`, `text/plain` 미디어 형식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="228d9-144">[Fiddler](https://www.telerik.com/fiddler) 또는 [Postman](https://www.getpostman.com/tools)과 같은 도구는 반환 형식을 지정하도록 `Accept` 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="228d9-145">`Accept` 헤더에 서버가 지원하는 형식이 포함되어 있으면 해당 형식이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="228d9-146">다음 섹션에서는 추가적인 포맷터를 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="228d9-147">컨트롤러 작업은 POCO(Plain Old CLR Objects)를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="228d9-148">POCO가 반환되면 런타임에서는 개체를 래핑하는`ObjectResult`를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="228d9-149">클라이언트는 형식이 지정된 직렬화된 개체를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="228d9-150">반환되는 개체가 `null`이면 `204 No Content` 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="228d9-151">개체 형식 반환하기:</span><span class="sxs-lookup"><span data-stu-id="228d9-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="228d9-152">위 코드에서는 유효한 작성자 별칭에 대한 요청이 작성자의 데이터와 함께 `200 OK` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="228d9-153">잘못된 별칭에 대한 요청은 `204 No Content` 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="228d9-154">Accept 헤더</span><span class="sxs-lookup"><span data-stu-id="228d9-154">The Accept header</span></span>

<span data-ttu-id="228d9-155">콘텐츠 *협상*은 `Accept` 헤더가 요청에 나타나는 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="228d9-156">요청에 Accept 헤더가 포함되어 있으면 ASP.NET Core는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="228d9-157">기본 설정 순서에 따라 Accept 헤더의 미디어 형식을 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="228d9-158">지정된 형식 중 하나로 응답을 생성할 수 있는 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="228d9-159">클라이언트의 요청을 충족할 수 있는 포맷터가 없는 경우 ASP.NET Core는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="228d9-160"><xref:Microsoft.AspNetCore.Mvc.MvcOptions>이 설정된 경우 `406 Not Acceptable`을 반환하거나</span><span class="sxs-lookup"><span data-stu-id="228d9-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="228d9-161">응답을 생성할 수 있는 첫 번째 포맷터를 찾으려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="228d9-162">요청된 형식에 대해 포맷터가 구성되지 않은 경우에는 개체의 서식을 지정할 수 있는 첫 번째 포맷터가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="228d9-163">요청에 `Accept` 헤더가 표시되지 않을 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="228d9-164">개체를 처리할 수 있는 첫 번째 포맷터는 응답을 직렬화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="228d9-165">발생하는 협상이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="228d9-166">서버에서 반환할 형식을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-166">The server is determining what format to return.</span></span>

<span data-ttu-id="228d9-167">Accept 헤더에 `*/*`가 포함되는 경우, `RespectBrowserAcceptHeader`가 <xref:Microsoft.AspNetCore.Mvc.MvcOptions>에서 true로 설정되지 않으면 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="228d9-168">브라우저 및 콘텐츠 협상</span><span class="sxs-lookup"><span data-stu-id="228d9-168">Browsers and content negotiation</span></span>

<span data-ttu-id="228d9-169">일반적인 API 클라이언트와 달리 웹 브라우저는 `Accept` 헤더를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="228d9-170">웹 브라우저는 와일드카드를 비롯한 다양한 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="228d9-171">기본적으로 프레임워크가 브라우저에서 요청이 수신되는 것을 감지할 경우 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="228d9-172">`Accept` 헤더는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="228d9-173">달리 구성되지 않은 경우 콘텐츠가 JSON에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="228d9-174">이것은 API를 소비할 때 브라우저에서 보다 일관된 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="228d9-175">브라우저 Accept 헤더를 적용하도록 앱을 구성하려면 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader>를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="228d9-176">포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="228d9-176">Configure formatters</span></span>

<span data-ttu-id="228d9-177">추가 형식을 지원해야 하는 앱은 적절한 NuGet 패키지를 추가하고 지원을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="228d9-178">입력 및 출력에 대한 개별 포맷터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="228d9-179">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="228d9-180">출력 포맷터는 응답의 형식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="228d9-181">사용자 지정 포맷터 생성에 대한 정보는 [사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="228d9-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="228d9-182">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="228d9-182">Add XML format support</span></span>

<span data-ttu-id="228d9-183"><xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="228d9-184">위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="228d9-185">위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="228d9-186">System.Text.Json 기반 포맷터 구성</span><span class="sxs-lookup"><span data-stu-id="228d9-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="228d9-187">`System.Text.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="228d9-188">`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="228d9-189">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="228d9-190">Newtonsoft.Json 기반 JSON 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="228d9-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="228d9-191">ASP.NET Core 3.0 이전에는 `Newtonsoft.Json` 패키지를 사용하여 구현된 JSON 포맷터를 기본적으로 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="228d9-192">ASP.NET Core 3.0 이후의 기본 JSON 포맷터는 `System.Text.Json`을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="228d9-193">`Newtonsoft.Json`기반 포맷터 및 기능에 대 한 지원은 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 패키지를 설치 하 고에서 구성 하 여 사용할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="228d9-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="228d9-194">일부 기능은 `System.Text.Json` 기반 포맷터와 잘 호환되지 않고 `Newtonsoft.Json` 기반 포맷터에 대한 참조를 필요로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="228d9-195">앱이 다음과 같은 경우 `Newtonsoft.Json` 기반 포맷터를 계속해서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="228d9-196">`Newtonsoft.Json` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="228d9-197">예를 들어 `[JsonProperty]` 또는 `[JsonIgnore]`로 이름을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="228d9-198">직렬화 설정을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="228d9-199">`Newtonsoft.Json`은 제공하는 기능에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="228d9-200">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`를 구성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="228d9-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="228d9-201">ASP.NET Core 3.0 이전의 `JsonResult.SerializerSettings`는 `Newtonsoft.Json` 고유의 `JsonSerializerSettings`의 인스턴스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="228d9-202">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 문서를 생성하는 경우.</span><span class="sxs-lookup"><span data-stu-id="228d9-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="228d9-203">`Newtonsoft.Json` 기반 포맷터의 기능은 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="228d9-204">`JsonResult`를 사용하여 동작 단위로 출력 serialization 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="228d9-205">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-205">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="228d9-206">XML 형식 지원 추가</span><span class="sxs-lookup"><span data-stu-id="228d9-206">Add XML format support</span></span>

<span data-ttu-id="228d9-207">XML 형식 지정은 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 패키지를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="228d9-208"><xref:System.Xml.Serialization.XmlSerializer>를 사용하여 구현된 XML 포맷터는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>를 호출하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="228d9-209">위의 코드는 `XmlSerializer`를 사용하여 결과를 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="228d9-210">위의 코드를 사용할 때 컨트롤러 메서드는 요청의 `Accept` 헤더에 따라 적절한 형식을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="228d9-211">형식 지정</span><span class="sxs-lookup"><span data-stu-id="228d9-211">Specify a format</span></span>

<span data-ttu-id="228d9-212">응답 형식을 제한 하려면 필터를 적용 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="228d9-213">대부분의 [필터](xref:mvc/controllers/filters)와 마찬가지로, `[Produces]` 작업, 컨트롤러 또는 전역 범위에서 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="228d9-214">이전 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 필터:</span><span class="sxs-lookup"><span data-stu-id="228d9-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="228d9-215">컨트롤러 내의 모든 작업이 JSON 형식 응답을 반환하도록 강제합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="228d9-216">다른 포맷터가 구성되고 클라이언트에서 다른 형식을 지정하는 경우 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="228d9-217">자세한 내용은 [필터](xref:mvc/controllers/filters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="228d9-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="228d9-218">특수한 사례 포맷터</span><span class="sxs-lookup"><span data-stu-id="228d9-218">Special case formatters</span></span>

<span data-ttu-id="228d9-219">일부 특수한 경우 기본 제공 포맷터를 사용하여 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="228d9-220">기본적으로 `string` 반환 형식은 *text/plain*(`Accept` 헤더를 통해 요청된 경우 *text/html*)으로 형식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="228d9-221">이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="228d9-222">포맷터들은 `ConfigureServices` 방법에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="228d9-223">모델 개체 반환 형식이 있는 작업은 `null`을 반환하는 경우 `204 No Content`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="228d9-224">이 동작은 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>를 제거하여 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="228d9-225">다음 코드를 `StringOutputFormatter` 및 `HttpNoContentOutputFormatter`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="228d9-226">`StringOutputFormatter`가 없으면 기본 제공 JSON 포맷터가 `string` 반환 형식의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="228d9-227">기본 제공 JSON 포맷터가 제거되고 XML 포맷터를 사용할 수 있는 경우, XML 포맷터가 `string` 반환 형식의 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="228d9-228">그렇지 않으면 `string` 반환 형식이 `406 Not Acceptable`을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="228d9-229">`HttpNoContentOutputFormatter`가 없으면 null 개체는 구성된 포맷터를 사용하여 서식이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="228d9-230">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-230">For example:</span></span>

* <span data-ttu-id="228d9-231">JSON 포맷터는 `null`의 본문이 포함된 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="228d9-232">XML 포맷터는 `xsi:nil="true"`로 설정된 특성을 사용하여 빈 XML 요소를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="228d9-233">응답 형식 URL 매핑</span><span class="sxs-lookup"><span data-stu-id="228d9-233">Response format URL mappings</span></span>

<span data-ttu-id="228d9-234">클라이언트는 URL의 일부로 특정 형식을 요청할 수 있습니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="228d9-235">쿼리 문자열 또는 경로의 부분에서.</span><span class="sxs-lookup"><span data-stu-id="228d9-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="228d9-236">.Xml 또는 .json과 같은 서식 지정 파일 확장명을 사용하여.</span><span class="sxs-lookup"><span data-stu-id="228d9-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="228d9-237">요청 경로의 매핑은 API가 사용하는 경로에 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="228d9-238">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="228d9-239">위 경로를 사용하면 요청된 형식을 선택적 파일 확장명으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="228d9-240">특성은에 [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) 형식 값이 있는지 확인 `RouteData` 하 고 응답이 생성 될 때 응답 형식을 적절 한 포맷터에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="228d9-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="228d9-241">경로</span><span class="sxs-lookup"><span data-stu-id="228d9-241">Route</span></span>        |             <span data-ttu-id="228d9-242">포맷터</span><span class="sxs-lookup"><span data-stu-id="228d9-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="228d9-243">기본 출력 포맷터</span><span class="sxs-lookup"><span data-stu-id="228d9-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="228d9-244">JSON 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="228d9-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="228d9-245">XML 포맷터(구성된 경우)</span><span class="sxs-lookup"><span data-stu-id="228d9-245">The XML formatter (if configured)</span></span>  |
