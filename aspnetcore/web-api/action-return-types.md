---
<span data-ttu-id="2012e-101">제목: ASP.NET Core web API author의 컨트롤러 작업 반환 형식: scottaddie description: ASP.NET Core web API에서 다양 한 컨트롤러 작업 메서드 반환 형식을 사용 하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="2012e-102">ms author: scaddie ms. custom: mvc ms. date: 02/03/2020 no loc:</span><span class="sxs-lookup"><span data-stu-id="2012e-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="2012e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2012e-103">'Blazor'</span></span>
- <span data-ttu-id="2012e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2012e-104">'Identity'</span></span>
- <span data-ttu-id="2012e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2012e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="2012e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2012e-106">'Razor'</span></span>
- <span data-ttu-id="2012e-107">' SignalR ' uid: 웹-a p i/작업-반환 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="2012e-108">ASP.NET Core 웹 API에서 컨트롤러 작업 반환 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="2012e-109">작성자: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2012e-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2012e-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2012e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2012e-111">ASP.NET Core에서는 웹 API 컨트롤러 작업 반환 형식에 다음 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="2012e-112">특정 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="2012e-113">IActionResult</span><span class="sxs-lookup"><span data-stu-id="2012e-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="2012e-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="2012e-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="2012e-115">특정 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="2012e-116">IActionResult</span><span class="sxs-lookup"><span data-stu-id="2012e-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="2012e-117">이 문서에서는 각 반환 형식을 사용하는 것이 가장 적합한 경우를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="2012e-118">특정 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-118">Specific type</span></span>

<span data-ttu-id="2012e-119">가장 간단한 작업은 기본 또는 복합 데이터 형식을 반환합니다(예: `string` 또는 사용자 지정 개체 형식).</span><span class="sxs-lookup"><span data-stu-id="2012e-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="2012e-120">다음 작업을 사용하여 사용자 지정 `Product` 개체의 컬렉션을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="2012e-121">작업 실행 중에 보호할 알려진 조건 없이 특정 형식을 반환하는 것으로 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="2012e-122">매개 변수 제약 조건 유효성 검사가 필요하지 않으므로 위의 작업은 매개 변수를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="2012e-123">여러 반환 형식을 사용할 수 있는 경우 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 반환 형식을 기본 또는 복합 반환 형식과 혼합 하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="2012e-124">이 동작 유형을 수용 하려면 [iactionresult](#iactionresult-type) 또는 [ \<T> actionresult](#actionresultt-type) 가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="2012e-125">이 문서에서는 여러 반환 형식에 대 한 몇 가지 샘플을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="2012e-126">IEnumerable \<T> 또는 IAsyncEnumerable을 반환 합니다.\<T></span><span class="sxs-lookup"><span data-stu-id="2012e-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="2012e-127">ASP.NET Core 2.2 이하 버전에서 작업에서 <xref:System.Collections.Generic.IEnumerable%601>를 반환하면 Serializer에 의한 동기 컬렉션 반복이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="2012e-128">그 결과는 호출 차단이며 스레드 풀 고갈의 가능성도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="2012e-129">예를 들어 웹 API의 데이터 액세스 요구 사항에 대해 EF(Entity Framework) Core를 사용하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="2012e-130">직렬화하는 동안 다음 작업의 반환 형식이 동기적으로 열거됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="2012e-131">ASP.NET Core 2.2 이하 버전에서 데이터베이스에 대한 동기 열거 및 차단 대기를 방지하려면 `ToListAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="2012e-132">ASP.NET Core 3.0 이상에서 작업이 `IAsyncEnumerable<T>`를 반환할 경우:</span><span class="sxs-lookup"><span data-stu-id="2012e-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="2012e-133">더 이상 동기 반복이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="2012e-134"><xref:System.Collections.Generic.IEnumerable%601>를 반환하는 것만큼 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="2012e-135">ASP.NET Core 3.0 이상에서는 Serializer에 제공하기 전에 다음 작업의 결과를 버퍼링합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="2012e-136">비동기 반복을 보장하기 위해 작업 서명의 반환 형식을 `IAsyncEnumerable<T>`로 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="2012e-137">궁극적으로 반복 모드는 반환되는 기본 구체적 형식을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="2012e-138">MVC는 `IAsyncEnumerable<T>`를 구현하는 모든 구체적 형식을 자동으로 버퍼링합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="2012e-139">다음과 같은 판매 가격이 책정된 제품 레코드를 `IEnumerable<Product>`로 반환하는 작업을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="2012e-140">다음은 위 작업의 `IAsyncEnumerable<Product>`에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="2012e-141">위의 두 작업은 모두 ASP.NET Core 3.0 기준으로 비차단입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="2012e-142">IActionResult 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-142">IActionResult type</span></span>

<span data-ttu-id="2012e-143">한 작업에 여러 `ActionResult` 반환 형식을 사용할 수 있는 경우 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 반환 형식이 적절합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="2012e-144">`ActionResult` 형식은 다양한 HTTP 상태 코드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="2012e-145">`ActionResult`에서 파생되는 비추상 클래스는 유효한 반환 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="2012e-146">이 범주에서 일반적인 반환 형식은 <xref:Microsoft.AspNetCore.Mvc.BadRequestResult>(400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult>(404) 및 <xref:Microsoft.AspNetCore.Mvc.OkObjectResult>(200)입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="2012e-147">또는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 클래스의 편의 메서드를 사용하여 작업에서 `ActionResult` 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="2012e-148">예를 들어 `return BadRequest();`는 `return new BadRequestResult();`의 약식 형태입니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="2012e-149">이러한 유형의 작업에는 여러 반환 형식 및 경로가 있으므로 특성을 자유롭게 사용 [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="2012e-150">이 특성은 [Swagger](xref:tutorials/web-api-help-pages-using-swagger)와 같은 도구에서 생성한 웹 API 도움말 페이지에 대한 설명이 포함된 응답 세부 정보를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="2012e-151">`[ProducesResponseType]`은 작업에서 반환한 알려진 형식 및 HTTP 상태 코드을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="2012e-152">동기화 작업</span><span class="sxs-lookup"><span data-stu-id="2012e-152">Synchronous action</span></span>

<span data-ttu-id="2012e-153">두 가지 가능한 반환 형식이 포함된 다음과 같은 동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="2012e-154">위 작업에서:</span><span class="sxs-lookup"><span data-stu-id="2012e-154">In the preceding action:</span></span>

* <span data-ttu-id="2012e-155">`id`에서 표시하는 제품이 내부 데이터 저장소에 없는 경우 404 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="2012e-156"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> 편의 메서드는 `return new NotFoundResult();`의 축약형으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="2012e-157">제품이 존재하는 경우 `Product` 개체와 함께 200 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="2012e-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 편의 메서드는 `return new OkObjectResult(product);`의 축약형으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="2012e-159">비동기 작업</span><span class="sxs-lookup"><span data-stu-id="2012e-159">Asynchronous action</span></span>

<span data-ttu-id="2012e-160">두 가지 가능한 반환 형식이 포함된 다음과 같은 비동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="2012e-161">위 작업에서:</span><span class="sxs-lookup"><span data-stu-id="2012e-161">In the preceding action:</span></span>

* <span data-ttu-id="2012e-162">제품 설명에 "XYZ 위젯"이 포함되어 있는 경우 400 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="2012e-163"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> 편의 메서드는 `return new BadRequestResult();`의 축약형으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="2012e-164">제품이 만들어지면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 편의 메서드가 201 상태 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="2012e-165">`CreatedAtAction`을 호출하는 대신 `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="2012e-166">이 코드 경로에서는 `Product` 개체가 응답 본문에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="2012e-167">새로 만든 제품의 URL을 포함하는 `Location` 응답 헤더가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="2012e-168">예를 들어 다음 모델은 요청이 `Name` 및 `Description` 속성을 포함해야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="2012e-169">요청에서 `Name` 및 `Description`을 제공하지 못하면 모델 유효성 검사에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2012e-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2.1 이상의 특성이 적용 되는 경우 모델 유효성 검사 오류는 400 상태 코드를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="2012e-171">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2012e-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="2012e-172">ActionResult \<T> 형식</span><span class="sxs-lookup"><span data-stu-id="2012e-172">ActionResult\<T> type</span></span>

<span data-ttu-id="2012e-173">ASP.NET Core 2.1에는 web API 컨트롤러 작업에 대 한 [Actionresult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) 반환 형식이 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="2012e-174">이를 통해 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 형식을 반환하거나 [특정 형식](#specific-type)을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="2012e-175">`ActionResult<T>`는 [IActionResult 형식](#iactionresult-type)을 통해 다음과 같은 혜택을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="2012e-176">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)특성의 `Type` 속성을 제외할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="2012e-177">예를 들어 `[ProducesResponseType(200, Type = typeof(Product))]`은 `[ProducesResponseType(200)]`으로 단순화됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="2012e-178">작업의 예상 반환 형식은 대신 `ActionResult<T>`의 `T`에서 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="2012e-179">[암시적 캐스트 연산자](/dotnet/csharp/language-reference/keywords/implicit)는 `T` 및 `ActionResult` 모두를 `ActionResult<T>`로 변환하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="2012e-180">`T`는 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>로 변환합니다. 즉, `return new ObjectResult(T);`는 `return T;`로 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="2012e-181">C#은 인터페이스에서 암시적 캐스트 연산자를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="2012e-182">따라서 인터페이스를 구체적인 형식으로 전환하려면 `ActionResult<T>`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="2012e-183">예를 들어 다음 예제에서 `IEnumerable`을 사용하면 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="2012e-184">이전 코드를 수정하는 한 가지 옵션으로 `_repository.GetProducts().ToList();`를 반환해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="2012e-185">대부분의 작업에는 특정 반환 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-185">Most actions have a specific return type.</span></span> <span data-ttu-id="2012e-186">작업을 실행하는 동안 예기치 않은 조건이 발생할 수 있습니다. 이 경우에 특정 형식이 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="2012e-187">예를 들어 작업의 입력 매개 변수는 모델 유효성 검사에 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="2012e-188">이러한 경우에 일반적으로 특정 형식이 아닌 적절한 `ActionResult` 형식을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="2012e-189">동기화 작업</span><span class="sxs-lookup"><span data-stu-id="2012e-189">Synchronous action</span></span>

<span data-ttu-id="2012e-190">두 가지 가능한 반환 형식이 포함된 동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="2012e-191">위 작업에서:</span><span class="sxs-lookup"><span data-stu-id="2012e-191">In the preceding action:</span></span>

* <span data-ttu-id="2012e-192">제품이 데이터베이스에 존재하지 않는 경우 404 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="2012e-193">제품이 존재하는 경우 해당 `Product` 개체와 함께 200 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="2012e-194">ASP.NET Core 2.1 이전에는 `return product;` 줄이 `return Ok(product);`이어야 했습니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="2012e-195">비동기 작업</span><span class="sxs-lookup"><span data-stu-id="2012e-195">Asynchronous action</span></span>

<span data-ttu-id="2012e-196">두 가지 가능한 반환 형식이 포함된 비동기 작업을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="2012e-197">위 작업에서:</span><span class="sxs-lookup"><span data-stu-id="2012e-197">In the preceding action:</span></span>

* <span data-ttu-id="2012e-198">다음과 같은 경우 ASP.NET Core 런타임에서 400 상태 코드(<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="2012e-199">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)특성이 적용 되었으며 모델 유효성 검사가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="2012e-200">제품 설명에 “XYZ 위젯”이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="2012e-201">제품이 만들어지면 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드가 201 상태 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="2012e-202">이 코드 경로에서는 `Product` 개체가 응답 본문에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="2012e-203">새로 만든 제품의 URL을 포함하는 `Location` 응답 헤더가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2012e-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2012e-204">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2012e-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
