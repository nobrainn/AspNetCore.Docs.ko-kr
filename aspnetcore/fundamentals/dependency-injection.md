---
<span data-ttu-id="e2092-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-102">'Blazor'</span></span>
- <span data-ttu-id="e2092-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-103">'Identity'</span></span>
- <span data-ttu-id="e2092-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-105">'Razor'</span></span>
- <span data-ttu-id="e2092-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="e2092-107">ASP.NET Core에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="e2092-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="e2092-108">작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="e2092-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2092-109">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e2092-110">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e2092-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2092-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e2092-112">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="e2092-112">Overview of dependency injection</span></span>

<span data-ttu-id="e2092-113">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e2092-114">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="e2092-115">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e2092-116">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="e2092-117">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e2092-118">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e2092-119">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e2092-120">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e2092-121">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e2092-122">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e2092-123">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e2092-124">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e2092-125">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e2092-126">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="e2092-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e2092-127">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e2092-128">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e2092-129">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="e2092-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e2092-130">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e2092-131">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e2092-132">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e2092-133">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="e2092-134">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e2092-135">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e2092-136">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e2092-137">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="e2092-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e2092-138">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e2092-139">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2092-140">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e2092-141">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="e2092-142">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e2092-143">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e2092-144">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="e2092-145">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e2092-146">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e2092-147">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e2092-148">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e2092-149">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="e2092-150">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e2092-151">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e2092-152">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="e2092-153">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-153">Services injected into Startup</span></span>

<span data-ttu-id="e2092-154">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e2092-155">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="e2092-156">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e2092-157">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-157">Framework-provided services</span></span>

<span data-ttu-id="e2092-158">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e2092-159">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e2092-160">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="e2092-161">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="e2092-162">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="e2092-162">Service Type</span></span> | <span data-ttu-id="e2092-163">수명</span><span class="sxs-lookup"><span data-stu-id="e2092-163">Lifetime</span></span> |
| ---
<span data-ttu-id="e2092-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-165">'Blazor'</span></span>
- <span data-ttu-id="e2092-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-166">'Identity'</span></span>
- <span data-ttu-id="e2092-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-168">'Razor'</span></span>
- <span data-ttu-id="e2092-169">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-171">'Blazor'</span></span>
- <span data-ttu-id="e2092-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-172">'Identity'</span></span>
- <span data-ttu-id="e2092-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-174">'Razor'</span></span>
- <span data-ttu-id="e2092-175">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-177">'Blazor'</span></span>
- <span data-ttu-id="e2092-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-178">'Identity'</span></span>
- <span data-ttu-id="e2092-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-180">'Razor'</span></span>
- <span data-ttu-id="e2092-181">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-183">'Blazor'</span></span>
- <span data-ttu-id="e2092-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-184">'Identity'</span></span>
- <span data-ttu-id="e2092-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-186">'Razor'</span></span>
- <span data-ttu-id="e2092-187">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-187">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-189">'Blazor'</span></span>
- <span data-ttu-id="e2092-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-190">'Identity'</span></span>
- <span data-ttu-id="e2092-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-192">'Razor'</span></span>
- <span data-ttu-id="e2092-193">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-195">'Blazor'</span></span>
- <span data-ttu-id="e2092-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-196">'Identity'</span></span>
- <span data-ttu-id="e2092-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-198">'Razor'</span></span>
- <span data-ttu-id="e2092-199">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-199">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="e2092-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="e2092-201">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="e2092-201">Register additional services with extension methods</span></span>

<span data-ttu-id="e2092-202">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e2092-203">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="e2092-204">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e2092-205">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="e2092-205">Service lifetimes</span></span>

<span data-ttu-id="e2092-206">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e2092-207">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="e2092-208">Transient</span><span class="sxs-lookup"><span data-stu-id="e2092-208">Transient</span></span>

<span data-ttu-id="e2092-209">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="e2092-210">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="e2092-211">Scoped</span><span class="sxs-lookup"><span data-stu-id="e2092-211">Scoped</span></span>

<span data-ttu-id="e2092-212">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="e2092-213">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e2092-214">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e2092-215">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e2092-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="e2092-216">Singleton</span></span>

<span data-ttu-id="e2092-217">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e2092-218">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e2092-219">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e2092-220">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="e2092-221">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e2092-222">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e2092-223">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="e2092-223">Service registration methods</span></span>

<span data-ttu-id="e2092-224">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="e2092-225">메서드</span><span class="sxs-lookup"><span data-stu-id="e2092-225">Method</span></span> | <span data-ttu-id="e2092-226">자동</span><span class="sxs-lookup"><span data-stu-id="e2092-226">Automatic</span></span><br><span data-ttu-id="e2092-227">개체</span><span class="sxs-lookup"><span data-stu-id="e2092-227">object</span></span><br><span data-ttu-id="e2092-228">삭제</span><span class="sxs-lookup"><span data-stu-id="e2092-228">disposal</span></span> | <span data-ttu-id="e2092-229">여러</span><span class="sxs-lookup"><span data-stu-id="e2092-229">Multiple</span></span><br><span data-ttu-id="e2092-230">구현</span><span class="sxs-lookup"><span data-stu-id="e2092-230">implementations</span></span> | <span data-ttu-id="e2092-231">인수 전달</span><span class="sxs-lookup"><span data-stu-id="e2092-231">Pass args</span></span> |
| ---
<span data-ttu-id="e2092-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-233">'Blazor'</span></span>
- <span data-ttu-id="e2092-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-234">'Identity'</span></span>
- <span data-ttu-id="e2092-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-236">'Razor'</span></span>
- <span data-ttu-id="e2092-237">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-237">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-239">'Blazor'</span></span>
- <span data-ttu-id="e2092-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-240">'Identity'</span></span>
- <span data-ttu-id="e2092-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-242">'Razor'</span></span>
- <span data-ttu-id="e2092-243">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-245">'Blazor'</span></span>
- <span data-ttu-id="e2092-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-246">'Identity'</span></span>
- <span data-ttu-id="e2092-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-248">'Razor'</span></span>
- <span data-ttu-id="e2092-249">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-251">'Blazor'</span></span>
- <span data-ttu-id="e2092-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-252">'Identity'</span></span>
- <span data-ttu-id="e2092-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-254">'Razor'</span></span>
- <span data-ttu-id="e2092-255">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-257">'Blazor'</span></span>
- <span data-ttu-id="e2092-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-258">'Identity'</span></span>
- <span data-ttu-id="e2092-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-260">'Razor'</span></span>
- <span data-ttu-id="e2092-261">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-263">'Blazor'</span></span>
- <span data-ttu-id="e2092-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-264">'Identity'</span></span>
- <span data-ttu-id="e2092-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-266">'Razor'</span></span>
- <span data-ttu-id="e2092-267">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-269">'Blazor'</span></span>
- <span data-ttu-id="e2092-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-270">'Identity'</span></span>
- <span data-ttu-id="e2092-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-272">'Razor'</span></span>
- <span data-ttu-id="e2092-273">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-275">'Blazor'</span></span>
- <span data-ttu-id="e2092-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-276">'Identity'</span></span>
- <span data-ttu-id="e2092-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-278">'Razor'</span></span>
- <span data-ttu-id="e2092-279">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-281">'Blazor'</span></span>
- <span data-ttu-id="e2092-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-282">'Identity'</span></span>
- <span data-ttu-id="e2092-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-284">'Razor'</span></span>
- <span data-ttu-id="e2092-285">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-287">'Blazor'</span></span>
- <span data-ttu-id="e2092-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-288">'Identity'</span></span>
- <span data-ttu-id="e2092-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-290">'Razor'</span></span>
- <span data-ttu-id="e2092-291">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-293">'Blazor'</span></span>
- <span data-ttu-id="e2092-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-294">'Identity'</span></span>
- <span data-ttu-id="e2092-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-296">'Razor'</span></span>
- <span data-ttu-id="e2092-297">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-299">'Blazor'</span></span>
- <span data-ttu-id="e2092-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-300">'Identity'</span></span>
- <span data-ttu-id="e2092-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-302">'Razor'</span></span>
- <span data-ttu-id="e2092-303">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-305">'Blazor'</span></span>
- <span data-ttu-id="e2092-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-306">'Identity'</span></span>
- <span data-ttu-id="e2092-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-308">'Razor'</span></span>
- <span data-ttu-id="e2092-309">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-309">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-311">'Blazor'</span></span>
- <span data-ttu-id="e2092-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-312">'Identity'</span></span>
- <span data-ttu-id="e2092-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-314">'Razor'</span></span>
- <span data-ttu-id="e2092-315">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-317">'Blazor'</span></span>
- <span data-ttu-id="e2092-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-318">'Identity'</span></span>
- <span data-ttu-id="e2092-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-320">'Razor'</span></span>
- <span data-ttu-id="e2092-321">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-323">'Blazor'</span></span>
- <span data-ttu-id="e2092-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-324">'Identity'</span></span>
- <span data-ttu-id="e2092-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-326">'Razor'</span></span>
- <span data-ttu-id="e2092-327">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-329">'Blazor'</span></span>
- <span data-ttu-id="e2092-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-330">'Identity'</span></span>
- <span data-ttu-id="e2092-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-332">'Razor'</span></span>
- <span data-ttu-id="e2092-333">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-335">'Blazor'</span></span>
- <span data-ttu-id="e2092-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-336">'Identity'</span></span>
- <span data-ttu-id="e2092-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-338">'Razor'</span></span>
- <span data-ttu-id="e2092-339">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-341">'Blazor'</span></span>
- <span data-ttu-id="e2092-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-342">'Identity'</span></span>
- <span data-ttu-id="e2092-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-344">'Razor'</span></span>
- <span data-ttu-id="e2092-345">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-347">'Blazor'</span></span>
- <span data-ttu-id="e2092-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-348">'Identity'</span></span>
- <span data-ttu-id="e2092-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-350">'Razor'</span></span>
- <span data-ttu-id="e2092-351">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-353">'Blazor'</span></span>
- <span data-ttu-id="e2092-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-354">'Identity'</span></span>
- <span data-ttu-id="e2092-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-356">'Razor'</span></span>
- <span data-ttu-id="e2092-357">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-359">'Blazor'</span></span>
- <span data-ttu-id="e2092-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-360">'Identity'</span></span>
- <span data-ttu-id="e2092-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-362">'Razor'</span></span>
- <span data-ttu-id="e2092-363">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-365">'Blazor'</span></span>
- <span data-ttu-id="e2092-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-366">'Identity'</span></span>
- <span data-ttu-id="e2092-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-368">'Razor'</span></span>
- <span data-ttu-id="e2092-369">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-369">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-371">'Blazor'</span></span>
- <span data-ttu-id="e2092-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-372">'Identity'</span></span>
- <span data-ttu-id="e2092-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-374">'Razor'</span></span>
- <span data-ttu-id="e2092-375">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-375">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="e2092-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="e2092-377">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-377">Example:</span></span><br><span data-ttu-id="e2092-378">`services.AddSingleton<IMyDep, MyDep>();` | 예 | 예 | 아니요 | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-379">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="e2092-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 예 | 예 | 예 | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="e2092-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="e2092-381">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-381">Example:</span></span><br><span data-ttu-id="e2092-382">`services.AddSingleton<MyDep>();` | 예 | 아니요 | 아니요 | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-383">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="e2092-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 아니요 | 예 | 예 | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-385">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="e2092-386">`services.AddSingleton(new MyDep("A string!"));` | 아니요 | 아니요 | 예 |</span><span class="sxs-lookup"><span data-stu-id="e2092-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="e2092-387">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e2092-388">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e2092-389">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e2092-390">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="e2092-391">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e2092-392">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="e2092-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e2092-394">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e2092-395">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e2092-396">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="e2092-397">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="e2092-398">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="e2092-399">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e2092-400">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="e2092-400">Constructor injection behavior</span></span>

<span data-ttu-id="e2092-401">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e2092-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e2092-403">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e2092-404">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e2092-405">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e2092-406">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e2092-407">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e2092-408">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="e2092-408">Entity Framework contexts</span></span>

<span data-ttu-id="e2092-409">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e2092-410">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="e2092-411">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e2092-412">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="e2092-412">Lifetime and registration options</span></span>

<span data-ttu-id="e2092-413">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e2092-414">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e2092-415">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e2092-416">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="e2092-417">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e2092-418">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e2092-419">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e2092-420">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e2092-421">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e2092-422">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="e2092-423">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e2092-424">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="e2092-425">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="e2092-426">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e2092-427">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="e2092-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="e2092-428">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e2092-429">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e2092-430">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="e2092-431">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e2092-432">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="e2092-432">**First request:**</span></span>

<span data-ttu-id="e2092-433">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-433">Controller operations:</span></span>

<span data-ttu-id="e2092-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e2092-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e2092-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e2092-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e2092-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-437">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-438">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-438">`OperationService` operations:</span></span>

<span data-ttu-id="e2092-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e2092-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e2092-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e2092-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e2092-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-442">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-443">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="e2092-443">**Second request:**</span></span>

<span data-ttu-id="e2092-444">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-444">Controller operations:</span></span>

<span data-ttu-id="e2092-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e2092-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e2092-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e2092-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e2092-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-448">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-449">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-449">`OperationService` operations:</span></span>

<span data-ttu-id="e2092-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e2092-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e2092-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e2092-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e2092-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-453">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-454">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e2092-455">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-455">*Transient* objects are always different.</span></span> <span data-ttu-id="e2092-456">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="e2092-457">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="e2092-458">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="e2092-459">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e2092-460">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="e2092-460">Call services from main</span></span>

<span data-ttu-id="e2092-461">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e2092-462">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e2092-463">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="e2092-464">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e2092-464">Scope validation</span></span>

<span data-ttu-id="e2092-465">앱이 개발 환경에서 실행 중이고 호스트를 빌드하기 위해 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)를 호출하는 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e2092-466">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="e2092-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e2092-467">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="e2092-468">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="e2092-469">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e2092-470">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e2092-471">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e2092-472">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e2092-473">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="e2092-474">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-474">Request Services</span></span>

<span data-ttu-id="e2092-475">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="e2092-476">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e2092-477">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e2092-478">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e2092-479">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="e2092-480">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e2092-481">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e2092-482">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="e2092-482">Design services for dependency injection</span></span>

<span data-ttu-id="e2092-483">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-483">Best practices are to:</span></span>

* <span data-ttu-id="e2092-484">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e2092-485">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e2092-486">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="e2092-487">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e2092-488">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e2092-489">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e2092-490">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e2092-491">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e2092-492">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e2092-493">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e2092-494">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="e2092-494">Disposal of services</span></span>

<span data-ttu-id="e2092-495">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e2092-496">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="e2092-497">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="e2092-498">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="e2092-498">In the following example:</span></span>

* <span data-ttu-id="e2092-499">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e2092-500">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="e2092-501">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e2092-502">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="e2092-503">서비스 삭제 옵션에 대한 자세한 내용은 [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)(ASP.NET Core에서 IDisposable을 삭제하는 4가지 방법)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="e2092-504">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="e2092-504">Default service container replacement</span></span>

<span data-ttu-id="e2092-505">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e2092-506">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="e2092-507">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="e2092-507">Property injection</span></span>
* <span data-ttu-id="e2092-508">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="e2092-508">Injection based on name</span></span>
* <span data-ttu-id="e2092-509">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="e2092-509">Child containers</span></span>
* <span data-ttu-id="e2092-510">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="e2092-510">Custom lifetime management</span></span>
* <span data-ttu-id="e2092-511">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="e2092-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e2092-512">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="e2092-512">Convention-based registration</span></span>

<span data-ttu-id="e2092-513">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e2092-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="e2092-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e2092-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="e2092-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e2092-516">유예</span><span class="sxs-lookup"><span data-stu-id="e2092-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e2092-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="e2092-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e2092-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="e2092-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e2092-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="e2092-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e2092-520">Unity</span><span class="sxs-lookup"><span data-stu-id="e2092-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="e2092-521">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="e2092-521">Thread safety</span></span>

<span data-ttu-id="e2092-522">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="e2092-523">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="e2092-524">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e2092-525">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e2092-526">권장 사항</span><span class="sxs-lookup"><span data-stu-id="e2092-526">Recommendations</span></span>

* <span data-ttu-id="e2092-527">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e2092-528">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e2092-529">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e2092-530">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e2092-531">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e2092-532">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="e2092-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e2092-533">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="e2092-534">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="e2092-535">‘서비스 로케이터 패턴’을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="e2092-536">예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService*>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="e2092-537">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="e2092-537">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="e2092-538">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="e2092-538">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="e2092-539">피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="e2092-540">이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="e2092-541">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="e2092-542">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e2092-543">예외는 드물게 발생하며, 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e2092-544">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e2092-545">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="e2092-546">DI의 다중 테넌트에 권장되는 패턴</span><span class="sxs-lookup"><span data-stu-id="e2092-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="e2092-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 다중 테넌트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="e2092-548">자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="e2092-549">CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 https://github.com/OrchardCMS/OrchardCore.Samples 에서 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2092-550">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2092-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e2092-551">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="e2092-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="e2092-552">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="e2092-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e2092-553">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="e2092-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="e2092-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="e2092-554">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2092-555">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="e2092-556">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="e2092-557">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2092-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="e2092-558">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="e2092-558">Overview of dependency injection</span></span>

<span data-ttu-id="e2092-559">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="e2092-560">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="e2092-561">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="e2092-562">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="e2092-563">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="e2092-564">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="e2092-565">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="e2092-566">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="e2092-567">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="e2092-568">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="e2092-569">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="e2092-570">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="e2092-571">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="e2092-572">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="e2092-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="e2092-573">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="e2092-574">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="e2092-575">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="e2092-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="e2092-576">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="e2092-577">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="e2092-578">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="e2092-579">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="e2092-580">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="e2092-581">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="e2092-582">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="e2092-583">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="e2092-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="e2092-584">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="e2092-585">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2092-586">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="e2092-587">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="e2092-588">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="e2092-589">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="e2092-590">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="e2092-591">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="e2092-592">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="e2092-593">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="e2092-594">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="e2092-595">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="e2092-596">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="e2092-597">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="e2092-598">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="e2092-599">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-599">Services injected into Startup</span></span>

<span data-ttu-id="e2092-600">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e2092-601">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="e2092-602">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e2092-603">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-603">Framework-provided services</span></span>

<span data-ttu-id="e2092-604">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="e2092-605">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="e2092-606">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="e2092-607">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="e2092-608">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="e2092-608">Service Type</span></span> | <span data-ttu-id="e2092-609">수명</span><span class="sxs-lookup"><span data-stu-id="e2092-609">Lifetime</span></span> |
| ---
<span data-ttu-id="e2092-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-611">'Blazor'</span></span>
- <span data-ttu-id="e2092-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-612">'Identity'</span></span>
- <span data-ttu-id="e2092-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-614">'Razor'</span></span>
- <span data-ttu-id="e2092-615">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-617">'Blazor'</span></span>
- <span data-ttu-id="e2092-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-618">'Identity'</span></span>
- <span data-ttu-id="e2092-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-620">'Razor'</span></span>
- <span data-ttu-id="e2092-621">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-623">'Blazor'</span></span>
- <span data-ttu-id="e2092-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-624">'Identity'</span></span>
- <span data-ttu-id="e2092-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-626">'Razor'</span></span>
- <span data-ttu-id="e2092-627">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-629">'Blazor'</span></span>
- <span data-ttu-id="e2092-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-630">'Identity'</span></span>
- <span data-ttu-id="e2092-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-632">'Razor'</span></span>
- <span data-ttu-id="e2092-633">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-633">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-635">'Blazor'</span></span>
- <span data-ttu-id="e2092-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-636">'Identity'</span></span>
- <span data-ttu-id="e2092-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-638">'Razor'</span></span>
- <span data-ttu-id="e2092-639">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-641">'Blazor'</span></span>
- <span data-ttu-id="e2092-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-642">'Identity'</span></span>
- <span data-ttu-id="e2092-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-644">'Razor'</span></span>
- <span data-ttu-id="e2092-645">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-645">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="e2092-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="e2092-647">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="e2092-647">Register additional services with extension methods</span></span>

<span data-ttu-id="e2092-648">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="e2092-649">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="e2092-650">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="e2092-651">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="e2092-651">Service lifetimes</span></span>

<span data-ttu-id="e2092-652">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="e2092-653">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="e2092-654">Transient</span><span class="sxs-lookup"><span data-stu-id="e2092-654">Transient</span></span>

<span data-ttu-id="e2092-655">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="e2092-656">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="e2092-657">Scoped</span><span class="sxs-lookup"><span data-stu-id="e2092-657">Scoped</span></span>

<span data-ttu-id="e2092-658">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="e2092-659">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="e2092-660">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="e2092-661">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="e2092-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="e2092-662">Singleton</span></span>

<span data-ttu-id="e2092-663">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="e2092-664">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="e2092-665">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="e2092-666">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="e2092-667">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="e2092-668">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="e2092-669">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="e2092-669">Service registration methods</span></span>

<span data-ttu-id="e2092-670">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="e2092-671">메서드</span><span class="sxs-lookup"><span data-stu-id="e2092-671">Method</span></span> | <span data-ttu-id="e2092-672">자동</span><span class="sxs-lookup"><span data-stu-id="e2092-672">Automatic</span></span><br><span data-ttu-id="e2092-673">개체</span><span class="sxs-lookup"><span data-stu-id="e2092-673">object</span></span><br><span data-ttu-id="e2092-674">삭제</span><span class="sxs-lookup"><span data-stu-id="e2092-674">disposal</span></span> | <span data-ttu-id="e2092-675">여러</span><span class="sxs-lookup"><span data-stu-id="e2092-675">Multiple</span></span><br><span data-ttu-id="e2092-676">구현</span><span class="sxs-lookup"><span data-stu-id="e2092-676">implementations</span></span> | <span data-ttu-id="e2092-677">인수 전달</span><span class="sxs-lookup"><span data-stu-id="e2092-677">Pass args</span></span> |
| ---
<span data-ttu-id="e2092-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-679">'Blazor'</span></span>
- <span data-ttu-id="e2092-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-680">'Identity'</span></span>
- <span data-ttu-id="e2092-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-682">'Razor'</span></span>
- <span data-ttu-id="e2092-683">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-683">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-685">'Blazor'</span></span>
- <span data-ttu-id="e2092-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-686">'Identity'</span></span>
- <span data-ttu-id="e2092-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-688">'Razor'</span></span>
- <span data-ttu-id="e2092-689">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-691">'Blazor'</span></span>
- <span data-ttu-id="e2092-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-692">'Identity'</span></span>
- <span data-ttu-id="e2092-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-694">'Razor'</span></span>
- <span data-ttu-id="e2092-695">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-697">'Blazor'</span></span>
- <span data-ttu-id="e2092-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-698">'Identity'</span></span>
- <span data-ttu-id="e2092-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-700">'Razor'</span></span>
- <span data-ttu-id="e2092-701">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-703">'Blazor'</span></span>
- <span data-ttu-id="e2092-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-704">'Identity'</span></span>
- <span data-ttu-id="e2092-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-706">'Razor'</span></span>
- <span data-ttu-id="e2092-707">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-709">'Blazor'</span></span>
- <span data-ttu-id="e2092-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-710">'Identity'</span></span>
- <span data-ttu-id="e2092-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-712">'Razor'</span></span>
- <span data-ttu-id="e2092-713">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-715">'Blazor'</span></span>
- <span data-ttu-id="e2092-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-716">'Identity'</span></span>
- <span data-ttu-id="e2092-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-718">'Razor'</span></span>
- <span data-ttu-id="e2092-719">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-721">'Blazor'</span></span>
- <span data-ttu-id="e2092-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-722">'Identity'</span></span>
- <span data-ttu-id="e2092-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-724">'Razor'</span></span>
- <span data-ttu-id="e2092-725">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-727">'Blazor'</span></span>
- <span data-ttu-id="e2092-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-728">'Identity'</span></span>
- <span data-ttu-id="e2092-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-730">'Razor'</span></span>
- <span data-ttu-id="e2092-731">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-733">'Blazor'</span></span>
- <span data-ttu-id="e2092-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-734">'Identity'</span></span>
- <span data-ttu-id="e2092-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-736">'Razor'</span></span>
- <span data-ttu-id="e2092-737">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-739">'Blazor'</span></span>
- <span data-ttu-id="e2092-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-740">'Identity'</span></span>
- <span data-ttu-id="e2092-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-742">'Razor'</span></span>
- <span data-ttu-id="e2092-743">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-745">'Blazor'</span></span>
- <span data-ttu-id="e2092-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-746">'Identity'</span></span>
- <span data-ttu-id="e2092-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-748">'Razor'</span></span>
- <span data-ttu-id="e2092-749">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-751">'Blazor'</span></span>
- <span data-ttu-id="e2092-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-752">'Identity'</span></span>
- <span data-ttu-id="e2092-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-754">'Razor'</span></span>
- <span data-ttu-id="e2092-755">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-755">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-757">'Blazor'</span></span>
- <span data-ttu-id="e2092-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-758">'Identity'</span></span>
- <span data-ttu-id="e2092-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-760">'Razor'</span></span>
- <span data-ttu-id="e2092-761">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-763">'Blazor'</span></span>
- <span data-ttu-id="e2092-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-764">'Identity'</span></span>
- <span data-ttu-id="e2092-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-766">'Razor'</span></span>
- <span data-ttu-id="e2092-767">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-769">'Blazor'</span></span>
- <span data-ttu-id="e2092-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-770">'Identity'</span></span>
- <span data-ttu-id="e2092-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-772">'Razor'</span></span>
- <span data-ttu-id="e2092-773">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-775">'Blazor'</span></span>
- <span data-ttu-id="e2092-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-776">'Identity'</span></span>
- <span data-ttu-id="e2092-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-778">'Razor'</span></span>
- <span data-ttu-id="e2092-779">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-781">'Blazor'</span></span>
- <span data-ttu-id="e2092-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-782">'Identity'</span></span>
- <span data-ttu-id="e2092-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-784">'Razor'</span></span>
- <span data-ttu-id="e2092-785">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-787">'Blazor'</span></span>
- <span data-ttu-id="e2092-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-788">'Identity'</span></span>
- <span data-ttu-id="e2092-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-790">'Razor'</span></span>
- <span data-ttu-id="e2092-791">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-793">'Blazor'</span></span>
- <span data-ttu-id="e2092-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-794">'Identity'</span></span>
- <span data-ttu-id="e2092-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-796">'Razor'</span></span>
- <span data-ttu-id="e2092-797">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-799">'Blazor'</span></span>
- <span data-ttu-id="e2092-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-800">'Identity'</span></span>
- <span data-ttu-id="e2092-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-802">'Razor'</span></span>
- <span data-ttu-id="e2092-803">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-805">'Blazor'</span></span>
- <span data-ttu-id="e2092-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-806">'Identity'</span></span>
- <span data-ttu-id="e2092-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-808">'Razor'</span></span>
- <span data-ttu-id="e2092-809">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e2092-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-811">'Blazor'</span></span>
- <span data-ttu-id="e2092-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-812">'Identity'</span></span>
- <span data-ttu-id="e2092-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-814">'Razor'</span></span>
- <span data-ttu-id="e2092-815">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-815">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e2092-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e2092-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e2092-817">'Blazor'</span></span>
- <span data-ttu-id="e2092-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e2092-818">'Identity'</span></span>
- <span data-ttu-id="e2092-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e2092-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="e2092-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e2092-820">'Razor'</span></span>
- <span data-ttu-id="e2092-821">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="e2092-821">'SignalR' uid:</span></span> 

<span data-ttu-id="e2092-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="e2092-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="e2092-823">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-823">Example:</span></span><br><span data-ttu-id="e2092-824">`services.AddSingleton<IMyDep, MyDep>();` | 예 | 예 | 아니요 | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-825">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="e2092-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 예 | 예 | 예 | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="e2092-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="e2092-827">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-827">Example:</span></span><br><span data-ttu-id="e2092-828">`services.AddSingleton<MyDep>();` | 예 | 아니요 | 아니요 | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-829">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="e2092-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 아니요 | 예 | 예 | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="e2092-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="e2092-831">예:</span><span class="sxs-lookup"><span data-stu-id="e2092-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="e2092-832">`services.AddSingleton(new MyDep("A string!"));` | 아니요 | 아니요 | 예 |</span><span class="sxs-lookup"><span data-stu-id="e2092-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="e2092-833">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="e2092-834">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="e2092-835">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="e2092-836">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="e2092-837">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="e2092-838">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="e2092-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="e2092-840">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="e2092-841">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="e2092-842">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="e2092-843">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="e2092-844">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="e2092-845">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="e2092-846">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="e2092-846">Constructor injection behavior</span></span>

<span data-ttu-id="e2092-847">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="e2092-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="e2092-849">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="e2092-850">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="e2092-851">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="e2092-852">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="e2092-853">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="e2092-854">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="e2092-854">Entity Framework contexts</span></span>

<span data-ttu-id="e2092-855">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="e2092-856">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="e2092-857">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="e2092-858">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="e2092-858">Lifetime and registration options</span></span>

<span data-ttu-id="e2092-859">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="e2092-860">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="e2092-861">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="e2092-862">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="e2092-863">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="e2092-864">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="e2092-865">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="e2092-866">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="e2092-867">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="e2092-868">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="e2092-869">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="e2092-870">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="e2092-871">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="e2092-872">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="e2092-873">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="e2092-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="e2092-874">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="e2092-875">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="e2092-876">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="e2092-877">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="e2092-878">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="e2092-878">**First request:**</span></span>

<span data-ttu-id="e2092-879">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-879">Controller operations:</span></span>

<span data-ttu-id="e2092-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="e2092-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="e2092-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e2092-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e2092-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-883">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-884">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-884">`OperationService` operations:</span></span>

<span data-ttu-id="e2092-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="e2092-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="e2092-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="e2092-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="e2092-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-888">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-889">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="e2092-889">**Second request:**</span></span>

<span data-ttu-id="e2092-890">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-890">Controller operations:</span></span>

<span data-ttu-id="e2092-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="e2092-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="e2092-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e2092-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e2092-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-894">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-895">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="e2092-895">`OperationService` operations:</span></span>

<span data-ttu-id="e2092-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="e2092-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="e2092-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="e2092-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="e2092-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="e2092-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="e2092-899">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="e2092-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="e2092-900">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="e2092-901">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-901">*Transient* objects are always different.</span></span> <span data-ttu-id="e2092-902">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="e2092-903">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="e2092-904">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="e2092-905">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="e2092-906">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="e2092-906">Call services from main</span></span>

<span data-ttu-id="e2092-907">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="e2092-908">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="e2092-909">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="e2092-910">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e2092-910">Scope validation</span></span>

<span data-ttu-id="e2092-911">앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="e2092-912">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="e2092-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="e2092-913">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="e2092-914">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="e2092-915">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="e2092-916">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="e2092-917">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="e2092-918">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="e2092-919">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="e2092-920">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="e2092-920">Request Services</span></span>

<span data-ttu-id="e2092-921">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="e2092-922">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="e2092-923">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="e2092-924">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="e2092-925">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="e2092-926">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="e2092-927">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="e2092-928">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="e2092-928">Design services for dependency injection</span></span>

<span data-ttu-id="e2092-929">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-929">Best practices are to:</span></span>

* <span data-ttu-id="e2092-930">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="e2092-931">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="e2092-932">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="e2092-933">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="e2092-934">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="e2092-935">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="e2092-936">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="e2092-937">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="e2092-938">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="e2092-939">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="e2092-940">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="e2092-940">Disposal of services</span></span>

<span data-ttu-id="e2092-941">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="e2092-942">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="e2092-943">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="e2092-944">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="e2092-944">In the following example:</span></span>

* <span data-ttu-id="e2092-945">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="e2092-946">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="e2092-947">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="e2092-948">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="e2092-949">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="e2092-949">Default service container replacement</span></span>

<span data-ttu-id="e2092-950">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="e2092-951">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="e2092-952">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="e2092-952">Property injection</span></span>
* <span data-ttu-id="e2092-953">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="e2092-953">Injection based on name</span></span>
* <span data-ttu-id="e2092-954">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="e2092-954">Child containers</span></span>
* <span data-ttu-id="e2092-955">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="e2092-955">Custom lifetime management</span></span>
* <span data-ttu-id="e2092-956">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="e2092-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="e2092-957">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="e2092-957">Convention-based registration</span></span>

<span data-ttu-id="e2092-958">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="e2092-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="e2092-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="e2092-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="e2092-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="e2092-961">유예</span><span class="sxs-lookup"><span data-stu-id="e2092-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="e2092-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="e2092-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="e2092-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="e2092-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="e2092-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="e2092-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="e2092-965">Unity</span><span class="sxs-lookup"><span data-stu-id="e2092-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="e2092-966">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="e2092-966">Thread safety</span></span>

<span data-ttu-id="e2092-967">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="e2092-968">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="e2092-969">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="e2092-970">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="e2092-971">권장 사항</span><span class="sxs-lookup"><span data-stu-id="e2092-971">Recommendations</span></span>

* <span data-ttu-id="e2092-972">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="e2092-973">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="e2092-974">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="e2092-975">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="e2092-976">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e2092-977">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="e2092-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="e2092-978">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="e2092-979">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="e2092-980">‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="e2092-981">DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="e2092-982">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="e2092-982">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="e2092-983">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="e2092-983">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="e2092-984">런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="e2092-985">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2092-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="e2092-986">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="e2092-987">예외는 드물게 발생하며, 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="e2092-988">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="e2092-989">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2092-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2092-990">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2092-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="e2092-991">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="e2092-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="e2092-992">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="e2092-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="e2092-993">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="e2092-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="e2092-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="e2092-994">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
