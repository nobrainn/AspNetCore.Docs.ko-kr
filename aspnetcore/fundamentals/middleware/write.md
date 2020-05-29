---
<span data-ttu-id="5e5ee-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5e5ee-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5e5ee-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5e5ee-102">'Blazor'</span></span>
- <span data-ttu-id="5e5ee-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5e5ee-103">'Identity'</span></span>
- <span data-ttu-id="5e5ee-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5e5ee-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5e5ee-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5e5ee-105">'Razor'</span></span>
- <span data-ttu-id="5e5ee-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="5e5ee-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="5e5ee-107">사용자 지정 ASP.NET Core 미들웨어 작성</span><span class="sxs-lookup"><span data-stu-id="5e5ee-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="5e5ee-108">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5e5ee-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5e5ee-109">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="5e5ee-110">ASP.NET Core는 풍부한 기본 제공 미들웨어 구성 요소 세트를 제공하지만 일부 시나리오에서는 사용자 지정 미들웨어를 작성하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="5e5ee-111">이 항목에서는 *규칙 기반* 미들웨어를 작성하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="5e5ee-112">강력한 형식화 및 요청당 활성화를 사용하는 방법에 대한 자세한 내용은 <xref:fundamentals/middleware/extensibility>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="5e5ee-113">미들웨어 클래스</span><span class="sxs-lookup"><span data-stu-id="5e5ee-113">Middleware class</span></span>

<span data-ttu-id="5e5ee-114">미들웨어는 일반적으로 클래스에서 캡슐화되고 확장 메서드로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="5e5ee-115">쿼리 문자열에서 현재 요청에 대한 문화권을 설정하는 다음 미들웨어를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="5e5ee-116">위의 샘플 코드는 미들웨어 구성 요소를 만드는 방법을 보여주는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="5e5ee-117">ASP.NET Core의 기본 제공 지역화 지원은 <xref:fundamentals/localization>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="5e5ee-118">문화권을 전달하여 미들웨어를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="5e5ee-119">예를 들어 `https://localhost:5001/?culture=no`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="5e5ee-120">다음 코드는 미들웨어 대리자를 클래스로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="5e5ee-121">미들웨어 클래스는 다음을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-121">The middleware class must include:</span></span>

* <span data-ttu-id="5e5ee-122"><xref:Microsoft.AspNetCore.Http.RequestDelegate> 형식의 매개 변수가 있는 공용 생성자</span><span class="sxs-lookup"><span data-stu-id="5e5ee-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="5e5ee-123">`Invoke` 또는 `InvokeAsync`라는 공용 메서드.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="5e5ee-124">이 메서드는 다음 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-124">This method must:</span></span>
  * <span data-ttu-id="5e5ee-125">`Task`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-125">Return a `Task`.</span></span>
  * <span data-ttu-id="5e5ee-126"><xref:Microsoft.AspNetCore.Http.HttpContext> 형식의 첫 번째 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="5e5ee-127">생성자 및 `Invoke`/`InvokeAsync`의 추가 매개 변수는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="5e5ee-128">미들웨어 종속성</span><span class="sxs-lookup"><span data-stu-id="5e5ee-128">Middleware dependencies</span></span>

<span data-ttu-id="5e5ee-129">미들웨어는 해당 생성자에서 해당 종속성을 노출하여 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따라야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="5e5ee-130">미들웨어는 *애플리케이션 수명*당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="5e5ee-131">요청 내에서 서비스를 미들웨어와 공유해야 하는 경우 [요청당 미들웨어 종속성](#per-request-middleware-dependencies) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="5e5ee-132">미들웨어 구성 요소는 생성자 매개 변수를 통해 [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 해당 종속성을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="5e5ee-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___)는 추가 매개 변수를 직접 수락할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="5e5ee-134">요청당 미들웨어 종속성</span><span class="sxs-lookup"><span data-stu-id="5e5ee-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="5e5ee-135">미들웨어는 요청당이 아닌 앱 시작 시 생성되므로 미들웨어 생성자에 의해 사용되는 *범위가 지정된* 수명 서비스는 각 요청 중에 다른 종속성 주입된 형식과 공유되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="5e5ee-136">*범위가 지정된* 서비스를 미들웨어와 다른 형식 간에 공유해야 하는 경우 이러한 서비스를 `Invoke` 메서드의 서명에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="5e5ee-137">`Invoke` 메서드는 DI로 채워지는 추가 매개 변수를 수락할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="5e5ee-138">미들웨어 확장 메서드</span><span class="sxs-lookup"><span data-stu-id="5e5ee-138">Middleware extension method</span></span>

<span data-ttu-id="5e5ee-139">다음 확장 메서드는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>를 통해 미들웨어를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="5e5ee-140">다음 코드는 `Startup.Configure`에서 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e5ee-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="5e5ee-141">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5e5ee-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
