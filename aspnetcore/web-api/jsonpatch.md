---
<span data-ttu-id="d3b59-101">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-102">'Blazor'</span></span>
- <span data-ttu-id="d3b59-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-103">'Identity'</span></span>
- <span data-ttu-id="d3b59-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-105">'Razor'</span></span>
- <span data-ttu-id="d3b59-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="d3b59-107">ASP.NET Core Web API의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="d3b59-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="d3b59-108">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d3b59-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3b59-109">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="d3b59-110">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="d3b59-110">Package installation</span></span>

<span data-ttu-id="d3b59-111">앱에서 JSON 패치 지원을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="d3b59-112">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="d3b59-113">프로젝트의 메서드를 업데이트 `Startup.ConfigureServices` 하 여를 호출 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="d3b59-114">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="d3b59-115">`AddNewtonsoftJson`은 MVC 서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="d3b59-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="d3b59-116">JSON Patch, AddNewtonsoftJson 및 System.object</span><span class="sxs-lookup"><span data-stu-id="d3b59-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="d3b59-117">`AddNewtonsoftJson``System.Text.Json` **모든** JSON 콘텐츠의 서식을 지정 하는 데 사용 되는 기반 입력 및 출력 포맷터를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="d3b59-118">를 사용 하 여 JSON 패치에 대 한 지원을 추가 하 고 `Newtonsoft.Json` 다른 포맷터는 변경 하지 않은 채 프로젝트의 메서드를 다음과 같이 업데이트 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d3b59-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="d3b59-119">위의 코드에는 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 패키지와 다음 문이 필요 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="d3b59-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="d3b59-120">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="d3b59-120">PATCH HTTP request method</span></span>

<span data-ttu-id="d3b59-121">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="d3b59-122">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="d3b59-123">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="d3b59-123">JSON Patch</span></span>

<span data-ttu-id="d3b59-124">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="d3b59-125">JSON 패치 문서에는 작업 배열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="d3b59-126">각 작업은 특정 유형의 변경 내용을 식별 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="d3b59-127">이러한 변경의 예로는 배열 요소 추가 또는 속성 값 바꾸기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="d3b59-128">예를 들어, 다음 JSON 문서는 리소스, 리소스에 대 한 JSON 패치 문서 및 패치 작업 적용 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="d3b59-129">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="d3b59-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="d3b59-130">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="d3b59-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="d3b59-131">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="d3b59-131">In the preceding JSON:</span></span>

* <span data-ttu-id="d3b59-132">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="d3b59-133">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="d3b59-134">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="d3b59-135">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="d3b59-135">Resource after patch</span></span>

<span data-ttu-id="d3b59-136">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="d3b59-137">JSON 패치 문서를 리소스에 적용 하 여 변경한 내용은 원자성입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="d3b59-138">목록에서 작업이 실패 하면 목록에 작업이 적용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="d3b59-139">경로 구문</span><span class="sxs-lookup"><span data-stu-id="d3b59-139">Path syntax</span></span>

<span data-ttu-id="d3b59-140">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="d3b59-141">`"/address/zipCode"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="d3b59-142">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="d3b59-143">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="d3b59-144">배열의 끝에 대 한 `add` `-` 인덱스 번호 대신 하이픈 ()을 사용 `/addresses/-` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="d3b59-145">작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-145">Operations</span></span>

<span data-ttu-id="d3b59-146">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="d3b59-147">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="d3b59-147">Operation</span></span>  | <span data-ttu-id="d3b59-148">메모</span><span class="sxs-lookup"><span data-stu-id="d3b59-148">Notes</span></span> |
|---
<span data-ttu-id="d3b59-149">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-150">'Blazor'</span></span>
- <span data-ttu-id="d3b59-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-151">'Identity'</span></span>
- <span data-ttu-id="d3b59-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-153">'Razor'</span></span>
- <span data-ttu-id="d3b59-154">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-155">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-156">'Blazor'</span></span>
- <span data-ttu-id="d3b59-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-157">'Identity'</span></span>
- <span data-ttu-id="d3b59-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-159">'Razor'</span></span>
- <span data-ttu-id="d3b59-160">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-161">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-162">'Blazor'</span></span>
- <span data-ttu-id="d3b59-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-163">'Identity'</span></span>
- <span data-ttu-id="d3b59-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-165">'Razor'</span></span>
- <span data-ttu-id="d3b59-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-166">'SignalR' uid:</span></span> 

<span data-ttu-id="d3b59-167">------|---
제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-168">'Blazor'</span></span>
- <span data-ttu-id="d3b59-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-169">'Identity'</span></span>
- <span data-ttu-id="d3b59-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-171">'Razor'</span></span>
- <span data-ttu-id="d3b59-172">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-173">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-174">'Blazor'</span></span>
- <span data-ttu-id="d3b59-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-175">'Identity'</span></span>
- <span data-ttu-id="d3b59-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-177">'Razor'</span></span>
- <span data-ttu-id="d3b59-178">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-179">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-180">'Blazor'</span></span>
- <span data-ttu-id="d3b59-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-181">'Identity'</span></span>
- <span data-ttu-id="d3b59-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-183">'Razor'</span></span>
- <span data-ttu-id="d3b59-184">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-185">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-186">'Blazor'</span></span>
- <span data-ttu-id="d3b59-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-187">'Identity'</span></span>
- <span data-ttu-id="d3b59-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-189">'Razor'</span></span>
- <span data-ttu-id="d3b59-190">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-191">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-192">'Blazor'</span></span>
- <span data-ttu-id="d3b59-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-193">'Identity'</span></span>
- <span data-ttu-id="d3b59-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-195">'Razor'</span></span>
- <span data-ttu-id="d3b59-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-197">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-198">'Blazor'</span></span>
- <span data-ttu-id="d3b59-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-199">'Identity'</span></span>
- <span data-ttu-id="d3b59-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-201">'Razor'</span></span>
- <span data-ttu-id="d3b59-202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-203">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-204">'Blazor'</span></span>
- <span data-ttu-id="d3b59-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-205">'Identity'</span></span>
- <span data-ttu-id="d3b59-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-207">'Razor'</span></span>
- <span data-ttu-id="d3b59-208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-209">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-210">'Blazor'</span></span>
- <span data-ttu-id="d3b59-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-211">'Identity'</span></span>
- <span data-ttu-id="d3b59-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-213">'Razor'</span></span>
- <span data-ttu-id="d3b59-214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-215">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-216">'Blazor'</span></span>
- <span data-ttu-id="d3b59-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-217">'Identity'</span></span>
- <span data-ttu-id="d3b59-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-219">'Razor'</span></span>
- <span data-ttu-id="d3b59-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-221">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-222">'Blazor'</span></span>
- <span data-ttu-id="d3b59-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-223">'Identity'</span></span>
- <span data-ttu-id="d3b59-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-225">'Razor'</span></span>
- <span data-ttu-id="d3b59-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-227">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-228">'Blazor'</span></span>
- <span data-ttu-id="d3b59-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-229">'Identity'</span></span>
- <span data-ttu-id="d3b59-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-231">'Razor'</span></span>
- <span data-ttu-id="d3b59-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-233">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-234">'Blazor'</span></span>
- <span data-ttu-id="d3b59-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-235">'Identity'</span></span>
- <span data-ttu-id="d3b59-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-237">'Razor'</span></span>
- <span data-ttu-id="d3b59-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-239">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-240">'Blazor'</span></span>
- <span data-ttu-id="d3b59-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-241">'Identity'</span></span>
- <span data-ttu-id="d3b59-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-243">'Razor'</span></span>
- <span data-ttu-id="d3b59-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-245">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-246">'Blazor'</span></span>
- <span data-ttu-id="d3b59-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-247">'Identity'</span></span>
- <span data-ttu-id="d3b59-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-249">'Razor'</span></span>
- <span data-ttu-id="d3b59-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-250">'SignalR' uid:</span></span> 

<span data-ttu-id="d3b59-251">----------------| | `add`     | 속성 또는 배열 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="d3b59-252">기존 속성: 값을 설정 합니다. | | `remove`  | 속성 또는 배열 요소를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="d3b59-253">| | `replace` | `remove`동일한 위치와 동일 `add` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="d3b59-254">| | `move`    | 원본 `remove` 에서의 값을 사용 하 여 대상 뒤에 오는 원본에서와 동일 `add` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="d3b59-255">| | `copy`    | `add`Source의 value를 사용 하는 대상과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="d3b59-256">| | `test`    | 의 값이 제공 된 경우 성공 상태 코드 `path` `value` 를 반환 합니다. |</span><span class="sxs-lookup"><span data-stu-id="d3b59-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="d3b59-257">ASP.NET Core의 JSON 패치</span><span class="sxs-lookup"><span data-stu-id="d3b59-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="d3b59-258">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="d3b59-259">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="d3b59-259">Action method code</span></span>

<span data-ttu-id="d3b59-260">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="d3b59-261">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="d3b59-262">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="d3b59-263">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="d3b59-264">예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="d3b59-265">샘플 앱의이 코드는 다음 모델과 함께 작동 합니다 `Customer` .</span><span class="sxs-lookup"><span data-stu-id="d3b59-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="d3b59-266">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="d3b59-266">The sample action method:</span></span>

* <span data-ttu-id="d3b59-267">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="d3b59-268">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-268">Applies the patch.</span></span>
* <span data-ttu-id="d3b59-269">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="d3b59-270">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="d3b59-271">모델 상태</span><span class="sxs-lookup"><span data-stu-id="d3b59-271">Model state</span></span>

<span data-ttu-id="d3b59-272">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="d3b59-273">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="d3b59-274">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="d3b59-275">동적 개체</span><span class="sxs-lookup"><span data-stu-id="d3b59-275">Dynamic objects</span></span>

<span data-ttu-id="d3b59-276">다음 동작 메서드 예에서는 동적 개체에 패치를 적용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="d3b59-277">추가 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-277">The add operation</span></span>

* <span data-ttu-id="d3b59-278">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="d3b59-279">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="d3b59-280">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="d3b59-281">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="d3b59-282">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="d3b59-283">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="d3b59-284">제거 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-284">The remove operation</span></span>

* <span data-ttu-id="d3b59-285">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="d3b59-286">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="d3b59-287">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="d3b59-288">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="d3b59-289">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="d3b59-290">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="d3b59-291">다음 샘플 패치 문서는 `CustomerName` 를 null로 설정 하 고 삭제 합니다 `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="d3b59-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="d3b59-292">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-292">The replace operation</span></span>

<span data-ttu-id="d3b59-293">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="d3b59-294">다음 샘플 패치 문서는의 값을 설정 `CustomerName` 하 고를 `Orders[0]` 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="d3b59-295">이동 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-295">The move operation</span></span>

* <span data-ttu-id="d3b59-296">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="d3b59-297">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="d3b59-298">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="d3b59-299">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="d3b59-300">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="d3b59-301">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-301">The following sample patch document:</span></span>

* <span data-ttu-id="d3b59-302">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="d3b59-303">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="d3b59-304">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="d3b59-305">복사 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-305">The copy operation</span></span>

<span data-ttu-id="d3b59-306">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="d3b59-307">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-307">The following sample patch document:</span></span>

* <span data-ttu-id="d3b59-308">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="d3b59-309">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="d3b59-310">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-310">The test operation</span></span>

<span data-ttu-id="d3b59-311">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="d3b59-312">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="d3b59-313">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="d3b59-314">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="d3b59-315">코드 다운로드</span><span class="sxs-lookup"><span data-stu-id="d3b59-315">Get the code</span></span>

<span data-ttu-id="d3b59-316">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)</span><span class="sxs-lookup"><span data-stu-id="d3b59-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="d3b59-317">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3b59-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d3b59-318">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="d3b59-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="d3b59-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="d3b59-320">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="d3b59-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="d3b59-321">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="d3b59-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="d3b59-322">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3b59-323">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d3b59-323">Additional resources</span></span>

* [<span data-ttu-id="d3b59-324">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="d3b59-325">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="d3b59-326">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="d3b59-327">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="d3b59-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="d3b59-328">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="d3b59-329">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="d3b59-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3b59-330">이 문서에서는 ASP.NET Core Web API에서 JSON 패치 요청을 처리하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="d3b59-331">PATCH HTTP 요청 메서드</span><span class="sxs-lookup"><span data-stu-id="d3b59-331">PATCH HTTP request method</span></span>

<span data-ttu-id="d3b59-332">PUT 및 [PATCH](https://tools.ietf.org/html/rfc5789) 메서드는 기존 리소스를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="d3b59-333">두 메서드의 차이점은 PUT은 전체 리소스를 바꾸지만, PATCH는 변경 내용만 지정한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="d3b59-334">JSON 패치</span><span class="sxs-lookup"><span data-stu-id="d3b59-334">JSON Patch</span></span>

<span data-ttu-id="d3b59-335">[JSON 패치](https://tools.ietf.org/html/rfc6902)는 리소스에 적용할 업데이트를 지정하기 위한 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="d3b59-336">JSON 패치 문서에는 작업 배열이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="d3b59-337">각 작업은 배열 요소 추가 또는 속성 값 바꾸기와 같은 특정 유형의 변경을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="d3b59-338">예를 들어 다음 JSON 문서는 리소스, 리소스의 JSON 패치 문서 및 패치 작업을 적용한 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="d3b59-339">리소스 예제</span><span class="sxs-lookup"><span data-stu-id="d3b59-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="d3b59-340">JSON 패치 예제</span><span class="sxs-lookup"><span data-stu-id="d3b59-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="d3b59-341">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="d3b59-341">In the preceding JSON:</span></span>

* <span data-ttu-id="d3b59-342">`op` 속성은 작업 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="d3b59-343">`path` 속성은 업데이트할 요소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="d3b59-344">`value` 속성은 새 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="d3b59-345">패치 후 리소스</span><span class="sxs-lookup"><span data-stu-id="d3b59-345">Resource after patch</span></span>

<span data-ttu-id="d3b59-346">앞의 JSON 패치 문서를 적용한 후 리소스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="d3b59-347">JSON 패치 문서를 리소스에 적용하여 변경된 내용은 원자성입니다. 목록에 있는 작업이 하나라도 실패하면 목록의 작업이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="d3b59-348">경로 구문</span><span class="sxs-lookup"><span data-stu-id="d3b59-348">Path syntax</span></span>

<span data-ttu-id="d3b59-349">작업 개체의 [path](https://tools.ietf.org/html/rfc6901) 속성에서 수준 사이에는 슬래시가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="d3b59-350">`"/address/zipCode"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="d3b59-351">0부터 시작하는 인덱스는 배열 요소를 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="d3b59-352">`addresses` 배열의 첫 번째 요소는 `/addresses/0`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="d3b59-353">배열 끝에 `add`(추가)하려면 인덱스 번호가 아닌 하이픈(-)을 사용합니다(`/addresses/-`).</span><span class="sxs-lookup"><span data-stu-id="d3b59-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="d3b59-354">작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-354">Operations</span></span>

<span data-ttu-id="d3b59-355">다음 표에서는 [JSON 패치 사양](https://tools.ietf.org/html/rfc6902)에 정의된 지원되는 작업을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="d3b59-356">작업(Operation)</span><span class="sxs-lookup"><span data-stu-id="d3b59-356">Operation</span></span>  | <span data-ttu-id="d3b59-357">메모</span><span class="sxs-lookup"><span data-stu-id="d3b59-357">Notes</span></span> |
|---
<span data-ttu-id="d3b59-358">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-359">'Blazor'</span></span>
- <span data-ttu-id="d3b59-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-360">'Identity'</span></span>
- <span data-ttu-id="d3b59-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-362">'Razor'</span></span>
- <span data-ttu-id="d3b59-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-364">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-365">'Blazor'</span></span>
- <span data-ttu-id="d3b59-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-366">'Identity'</span></span>
- <span data-ttu-id="d3b59-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-368">'Razor'</span></span>
- <span data-ttu-id="d3b59-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-370">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-371">'Blazor'</span></span>
- <span data-ttu-id="d3b59-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-372">'Identity'</span></span>
- <span data-ttu-id="d3b59-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-374">'Razor'</span></span>
- <span data-ttu-id="d3b59-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-375">'SignalR' uid:</span></span> 

<span data-ttu-id="d3b59-376">------|---
제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-377">'Blazor'</span></span>
- <span data-ttu-id="d3b59-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-378">'Identity'</span></span>
- <span data-ttu-id="d3b59-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-380">'Razor'</span></span>
- <span data-ttu-id="d3b59-381">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-382">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-383">'Blazor'</span></span>
- <span data-ttu-id="d3b59-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-384">'Identity'</span></span>
- <span data-ttu-id="d3b59-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-386">'Razor'</span></span>
- <span data-ttu-id="d3b59-387">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-388">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-389">'Blazor'</span></span>
- <span data-ttu-id="d3b59-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-390">'Identity'</span></span>
- <span data-ttu-id="d3b59-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-392">'Razor'</span></span>
- <span data-ttu-id="d3b59-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-394">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-395">'Blazor'</span></span>
- <span data-ttu-id="d3b59-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-396">'Identity'</span></span>
- <span data-ttu-id="d3b59-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-398">'Razor'</span></span>
- <span data-ttu-id="d3b59-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-400">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-401">'Blazor'</span></span>
- <span data-ttu-id="d3b59-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-402">'Identity'</span></span>
- <span data-ttu-id="d3b59-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-404">'Razor'</span></span>
- <span data-ttu-id="d3b59-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-406">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-407">'Blazor'</span></span>
- <span data-ttu-id="d3b59-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-408">'Identity'</span></span>
- <span data-ttu-id="d3b59-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-410">'Razor'</span></span>
- <span data-ttu-id="d3b59-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-412">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-413">'Blazor'</span></span>
- <span data-ttu-id="d3b59-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-414">'Identity'</span></span>
- <span data-ttu-id="d3b59-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-416">'Razor'</span></span>
- <span data-ttu-id="d3b59-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-418">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-419">'Blazor'</span></span>
- <span data-ttu-id="d3b59-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-420">'Identity'</span></span>
- <span data-ttu-id="d3b59-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-422">'Razor'</span></span>
- <span data-ttu-id="d3b59-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-424">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-425">'Blazor'</span></span>
- <span data-ttu-id="d3b59-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-426">'Identity'</span></span>
- <span data-ttu-id="d3b59-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-428">'Razor'</span></span>
- <span data-ttu-id="d3b59-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-430">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-431">'Blazor'</span></span>
- <span data-ttu-id="d3b59-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-432">'Identity'</span></span>
- <span data-ttu-id="d3b59-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-434">'Razor'</span></span>
- <span data-ttu-id="d3b59-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-436">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-437">'Blazor'</span></span>
- <span data-ttu-id="d3b59-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-438">'Identity'</span></span>
- <span data-ttu-id="d3b59-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-440">'Razor'</span></span>
- <span data-ttu-id="d3b59-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-442">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-443">'Blazor'</span></span>
- <span data-ttu-id="d3b59-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-444">'Identity'</span></span>
- <span data-ttu-id="d3b59-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-446">'Razor'</span></span>
- <span data-ttu-id="d3b59-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-448">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-449">'Blazor'</span></span>
- <span data-ttu-id="d3b59-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-450">'Identity'</span></span>
- <span data-ttu-id="d3b59-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-452">'Razor'</span></span>
- <span data-ttu-id="d3b59-453">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d3b59-454">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="d3b59-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d3b59-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-455">'Blazor'</span></span>
- <span data-ttu-id="d3b59-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d3b59-456">'Identity'</span></span>
- <span data-ttu-id="d3b59-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d3b59-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="d3b59-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d3b59-458">'Razor'</span></span>
- <span data-ttu-id="d3b59-459">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="d3b59-459">'SignalR' uid:</span></span> 

<span data-ttu-id="d3b59-460">----------------| | `add`     | 속성 또는 배열 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="d3b59-461">기존 속성: 값을 설정 합니다. | | `remove`  | 속성 또는 배열 요소를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="d3b59-462">| | `replace` | `remove`동일한 위치와 동일 `add` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="d3b59-463">| | `move`    | 원본 `remove` 에서의 값을 사용 하 여 대상 뒤에 오는 원본에서와 동일 `add` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="d3b59-464">| | `copy`    | `add`Source의 value를 사용 하는 대상과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="d3b59-465">| | `test`    | 의 값이 제공 된 경우 성공 상태 코드 `path` `value` 를 반환 합니다. |</span><span class="sxs-lookup"><span data-stu-id="d3b59-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="d3b59-466">ASP.NET Core의 JsonPatch</span><span class="sxs-lookup"><span data-stu-id="d3b59-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="d3b59-467">JSON 패치의 ASP.NET Core 구현은 [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="d3b59-468">패키지는 [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) 메타패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="d3b59-469">작업 메서드 코드</span><span class="sxs-lookup"><span data-stu-id="d3b59-469">Action method code</span></span>

<span data-ttu-id="d3b59-470">API 컨트롤러에서 JSON 패치의 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="d3b59-471">`HttpPatch` 특성을 사용하여 주석으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="d3b59-472">일반적으로 `[FromBody]`를 사용하여 `JsonPatchDocument<T>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="d3b59-473">패치 문서에서 `ApplyTo`를 호출하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="d3b59-474">예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="d3b59-475">샘플 앱의 이 코드는 다음 `Customer` 모델에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="d3b59-476">샘플 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="d3b59-476">The sample action method:</span></span>

* <span data-ttu-id="d3b59-477">`Customer`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="d3b59-478">패치를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-478">Applies the patch.</span></span>
* <span data-ttu-id="d3b59-479">응답의 본문으로 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="d3b59-480">실제 앱에서 이 코드는 데이터베이스와 같은 저장소에서 데이터를 검색하고 패치를 적용한 후 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="d3b59-481">모델 상태</span><span class="sxs-lookup"><span data-stu-id="d3b59-481">Model state</span></span>

<span data-ttu-id="d3b59-482">앞의 작업 메서드 예제에서는 모델 상태를 매개 변수 중 하나로 가져오는 `ApplyTo`의 오버로드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="d3b59-483">이 옵션을 사용하여 응답으로 오류 메시지를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="d3b59-484">다음 예제에서는 `test` 작업에 대한 400 잘못된 요청 응답의 본문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="d3b59-485">동적 개체</span><span class="sxs-lookup"><span data-stu-id="d3b59-485">Dynamic objects</span></span>

<span data-ttu-id="d3b59-486">다음 작업 메서드 예제에서는 동적 개체에 패치를 적용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="d3b59-487">추가 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-487">The add operation</span></span>

* <span data-ttu-id="d3b59-488">`path`가 배열 요소를 가리키는 경우: `path`에 지정된 요소 앞에 새 요소를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="d3b59-489">`path`가 속성을 가리키는 경우: 속성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="d3b59-490">`path`가 존재하지 않는 위치를 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="d3b59-491">패치할 리소스가 동적 개체인 경우: 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="d3b59-492">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="d3b59-493">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Order` 개체를 `Orders` 배열 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="d3b59-494">제거 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-494">The remove operation</span></span>

* <span data-ttu-id="d3b59-495">`path`가 배열 요소를 가리키는 경우: 요소를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="d3b59-496">`path`가 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="d3b59-497">패치할 리소스가 동적 개체인 경우: 속성을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="d3b59-498">패치할 리소스가 정적 개체인 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="d3b59-499">속성이 nullable인 경우: null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="d3b59-500">속성이 nullable이 아닌 경우: `default<T>`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="d3b59-501">다음 샘플 패치 문서는 `CustomerName`을 null로 설정하고 `Orders[0]`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="d3b59-502">바꾸기 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-502">The replace operation</span></span>

<span data-ttu-id="d3b59-503">이 작업은 `add`가 뒤에 오는 `remove`와 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="d3b59-504">다음 샘플 패치 문서는 `CustomerName`의 값을 설정하고 `Orders[0]`를 새 `Order` 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="d3b59-505">이동 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-505">The move operation</span></span>

* <span data-ttu-id="d3b59-506">`path`가 배열 요소를 가리키는 경우: `from` 요소를 `path` 요소 위치에 복사한 후 `from` 요소에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="d3b59-507">`path`가 속성을 가리키는 경우: `from` 속성 값을 `path` 속성에 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="d3b59-508">`path`가 존재하지 않는 속성을 가리키는 경우:</span><span class="sxs-lookup"><span data-stu-id="d3b59-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="d3b59-509">패치할 리소스가 정적 개체인 경우: 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="d3b59-510">패치할 리소스가 동적 개체인 경우: `from` 속성을 `path`에 지정된 위치로 복사한 후 `from` 속성에서 `remove` 작업을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="d3b59-511">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-511">The following sample patch document:</span></span>

* <span data-ttu-id="d3b59-512">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="d3b59-513">`Orders[0].OrderName`을 null로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="d3b59-514">`Orders[1]`를 `Orders[0]` 앞으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="d3b59-515">복사 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-515">The copy operation</span></span>

<span data-ttu-id="d3b59-516">이 작업은 마지막 `remove` 단계 없이 `move` 작업과 기능적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="d3b59-517">다음 샘플 패치 문서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-517">The following sample patch document:</span></span>

* <span data-ttu-id="d3b59-518">`Orders[0].OrderName` 값을 `CustomerName`에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="d3b59-519">`Orders[1]` 복사본을 `Orders[0]` 앞에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="d3b59-520">테스트 작업</span><span class="sxs-lookup"><span data-stu-id="d3b59-520">The test operation</span></span>

<span data-ttu-id="d3b59-521">`path`에 지정된 위치의 값이 `value`에 제공된 값과 다른 경우 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="d3b59-522">이 경우 패치 문서의 다른 모든 작업이 성공하더라도 전체 PATCH 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="d3b59-523">`test` 작업은 일반적으로 동시성 충돌이 발생하는 경우 업데이트를 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="d3b59-524">`CustomerName`의 초기 값이 “John”인 경우 테스트에 실패하므로 다음 샘플 패치 문서는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="d3b59-525">코드 다운로드</span><span class="sxs-lookup"><span data-stu-id="d3b59-525">Get the code</span></span>

<span data-ttu-id="d3b59-526">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="d3b59-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="d3b59-527">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3b59-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d3b59-528">샘플을 테스트하려면 앱을 실행하고 다음 설정을 사용하여 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="d3b59-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="d3b59-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="d3b59-530">HTTP 메서드: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="d3b59-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="d3b59-531">헤더: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="d3b59-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="d3b59-532">Body: *json 프로젝트 폴더에서 json 패치* 문서 샘플 중 하나를 복사 하 여 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3b59-533">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d3b59-533">Additional resources</span></span>

* [<span data-ttu-id="d3b59-534">IETF RFC 5789 PATCH 메서드 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="d3b59-535">IETF RFC 6902 JSON 패치 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="d3b59-536">IETF RFC 6901 JSON 패치 경로 형식 사양</span><span class="sxs-lookup"><span data-stu-id="d3b59-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="d3b59-537">[JSON 패치 문서](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="d3b59-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="d3b59-538">JSON 패치 문서를 만들기 위한 리소스의 링크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3b59-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="d3b59-539">ASP.NET Core JSON 패치 소스 코드</span><span class="sxs-lookup"><span data-stu-id="d3b59-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
