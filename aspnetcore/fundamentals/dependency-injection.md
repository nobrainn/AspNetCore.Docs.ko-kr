---
title: '제목: ASP.NET Core의 종속성 주입 작성자: rick-anderson 설명: ASP.NET Core에서 종속성 주입을 구현하는 방법 및 사용 방법에 알아봅니다.'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 05/14/2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271906"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="ecb4c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ecb4c-103">'Blazor'</span></span>

<span data-ttu-id="ecb4c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ecb4c-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ecb4c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ecb4c-105">'Let's Encrypt'</span></span>

<span data-ttu-id="ecb4c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ecb4c-106">'Razor'</span></span>

<span data-ttu-id="ecb4c-107">'SignalR' uid: fundamentals/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="ecb4c-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="ecb4c-108">ASP.NET Core에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="ecb4c-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="ecb4c-109">작성자: [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="ecb4c-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span> <span data-ttu-id="ecb4c-110">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="ecb4c-111">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="ecb4c-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ecb4c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="ecb4c-113">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="ecb4c-113">Overview of dependency injection</span></span> <span data-ttu-id="ecb4c-114">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-114">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="ecb4c-115">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="ecb4c-116">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ecb4c-117">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="ecb4c-118">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ecb4c-119">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="ecb4c-120">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="ecb4c-121">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="ecb4c-122">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="ecb4c-123">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ecb4c-124">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="ecb4c-125">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-125">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="ecb4c-126">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-126">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="ecb4c-127">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-127">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ecb4c-128">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ecb4c-129">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ecb4c-130">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ecb4c-131">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="ecb4c-132">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="ecb4c-133">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="ecb4c-134">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ecb4c-135">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ecb4c-136">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-136">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="ecb4c-137">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ecb4c-138">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="ecb4c-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="ecb4c-139">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ecb4c-140">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ecb4c-141">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="ecb4c-142">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="ecb4c-143">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ecb4c-144">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="ecb4c-145">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="ecb4c-146">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ecb4c-147">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="ecb4c-148">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-148">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ecb4c-149">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ecb4c-150">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ecb4c-151">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ecb4c-152">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-152">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ecb4c-153">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="ecb4c-154">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-154">Services injected into Startup</span></span> <span data-ttu-id="ecb4c-155">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="ecb4c-156">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-156">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="ecb4c-157">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-157">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="ecb4c-158">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-158">Framework-provided services</span></span> | <span data-ttu-id="ecb4c-159">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-160">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="ecb4c-161">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="ecb4c-162">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-162">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ecb4c-163">서비스 유형</span><span class="sxs-lookup"><span data-stu-id="ecb4c-163">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ecb4c-164">수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-164">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ecb4c-165">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-165">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-166">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ecb4c-169">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-169">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-170">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-170">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-171">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-171">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ecb4c-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ecb4c-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ecb4c-174">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-174">Singleton</span></span>

<span data-ttu-id="ecb4c-175">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-175">Transient</span></span> <span data-ttu-id="ecb4c-176">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-176">Singleton</span></span>

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

<span data-ttu-id="ecb4c-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-177">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ecb4c-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-178">Singleton</span></span>

<span data-ttu-id="ecb4c-179">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="ecb4c-179">Register additional services with extension methods</span></span> <span data-ttu-id="ecb4c-180">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="ecb4c-181">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="ecb4c-182">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="ecb4c-183">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-183">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="ecb4c-184">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-184">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="ecb4c-185">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="ecb4c-186">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-186">Transient</span></span> <span data-ttu-id="ecb4c-187">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ecb4c-188">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="ecb4c-189">Scoped</span><span class="sxs-lookup"><span data-stu-id="ecb4c-189">Scoped</span></span>

<span data-ttu-id="ecb4c-190">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="ecb4c-191">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ecb4c-192">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ecb4c-193">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="ecb4c-194">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-194">Singleton</span></span> <span data-ttu-id="ecb4c-195">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ecb4c-196">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-196">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="ecb4c-197">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="ecb4c-198">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="ecb4c-199">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-199">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="ecb4c-200">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="ecb4c-201">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="ecb4c-201">Service registration methods</span></span> | <span data-ttu-id="ecb4c-202">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="ecb4c-203">방법</span><span class="sxs-lookup"><span data-stu-id="ecb4c-203">Method</span></span> | <span data-ttu-id="ecb4c-204">자동</span><span class="sxs-lookup"><span data-stu-id="ecb4c-204">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ecb4c-205">개체</span><span class="sxs-lookup"><span data-stu-id="ecb4c-205">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ecb4c-206">삭제</span><span class="sxs-lookup"><span data-stu-id="ecb4c-206">disposal</span></span> | <span data-ttu-id="ecb4c-207">여러</span><span class="sxs-lookup"><span data-stu-id="ecb4c-207">Multiple</span></span> | <span data-ttu-id="ecb4c-208">구현</span><span class="sxs-lookup"><span data-stu-id="ecb4c-208">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-209">인수 전달</span><span class="sxs-lookup"><span data-stu-id="ecb4c-209">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ecb4c-210">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-210">Example:</span></span> | <span data-ttu-id="ecb4c-211">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-211">Yes</span></span> | <span data-ttu-id="ecb4c-212">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ecb4c-213">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-213">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ecb4c-214">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-214">Examples:</span></span> | <span data-ttu-id="ecb4c-215">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-215">Yes</span></span> | <span data-ttu-id="ecb4c-216">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-216">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-217">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-217">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ecb4c-218">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-218">Example:</span></span> | <span data-ttu-id="ecb4c-219">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-219">Yes</span></span> | <span data-ttu-id="ecb4c-220">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-220">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-221">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-221">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ecb4c-222">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-222">Examples:</span></span> | <span data-ttu-id="ecb4c-223">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-223">No</span></span> | <span data-ttu-id="ecb4c-224">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-224">Yes</span></span> |

<span data-ttu-id="ecb4c-225">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-225">Yes</span></span> <span data-ttu-id="ecb4c-226">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-226">Examples:</span></span>

<span data-ttu-id="ecb4c-227">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-227">No</span></span>

<span data-ttu-id="ecb4c-228">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-228">No</span></span> <span data-ttu-id="ecb4c-229">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-229">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ecb4c-230">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ecb4c-231">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="ecb4c-232">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="ecb4c-233">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="ecb4c-234">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="ecb4c-235">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-235">For more information, see:</span></span> <span data-ttu-id="ecb4c-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ecb4c-237">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ecb4c-238">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="ecb4c-239">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ecb4c-240">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ecb4c-241">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-241">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="ecb4c-242">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="ecb4c-243">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="ecb4c-243">Constructor injection behavior</span></span>

<span data-ttu-id="ecb4c-244">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-244">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="ecb4c-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ecb4c-246">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ecb4c-247">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="ecb4c-248">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="ecb4c-249">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ecb4c-250">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="ecb4c-251">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="ecb4c-251">Entity Framework contexts</span></span> <span data-ttu-id="ecb4c-252">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ecb4c-253">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ecb4c-254">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ecb4c-255">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="ecb4c-255">Lifetime and registration options</span></span> <span data-ttu-id="ecb4c-256">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="ecb4c-257">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="ecb4c-258">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ecb4c-259">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="ecb4c-260">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ecb4c-261">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="ecb4c-262">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ecb4c-263">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ecb4c-264">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-264">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="ecb4c-265">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="ecb4c-266">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-266">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="ecb4c-267">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="ecb4c-268">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ecb4c-269">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="ecb4c-270">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="ecb4c-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ecb4c-271">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="ecb4c-272">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="ecb4c-273">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="ecb4c-274">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-274">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="ecb4c-275">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="ecb4c-275">**First request:**</span></span>

<span data-ttu-id="ecb4c-276">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-276">Controller operations:</span></span>

<span data-ttu-id="ecb4c-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ecb4c-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ecb4c-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ecb4c-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ecb4c-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-280">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ecb4c-281">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-281">`OperationService` operations:</span></span>

<span data-ttu-id="ecb4c-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ecb4c-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="ecb4c-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ecb4c-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ecb4c-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-285">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="ecb4c-286">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-286">**Second request:**</span></span>

<span data-ttu-id="ecb4c-287">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-287">Controller operations:</span></span>

<span data-ttu-id="ecb4c-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ecb4c-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ecb4c-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ecb4c-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ecb4c-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-291">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ecb4c-292">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-292">`OperationService` operations:</span></span>

* <span data-ttu-id="ecb4c-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ecb4c-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="ecb4c-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ecb4c-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="ecb4c-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="ecb4c-296">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="ecb4c-297">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ecb4c-298">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-298">*Transient* objects are always different.</span></span>

<span data-ttu-id="ecb4c-299">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ecb4c-300">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-300">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="ecb4c-301">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ecb4c-302">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ecb4c-303">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="ecb4c-303">Call services from main</span></span>

* <span data-ttu-id="ecb4c-304">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="ecb4c-305">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="ecb4c-306">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="ecb4c-307">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="ecb4c-307">Scope validation</span></span>

<span data-ttu-id="ecb4c-308">앱이 개발 환경에서 실행 중이고 호스트를 빌드하기 위해 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)를 호출하는 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="ecb4c-309">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="ecb4c-310">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ecb4c-311">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="ecb4c-312">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ecb4c-313">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-313">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="ecb4c-314">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ecb4c-315">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ecb4c-316">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="ecb4c-317">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-317">Request Services</span></span> <span data-ttu-id="ecb4c-318">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="ecb4c-319">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-319">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ecb4c-320">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ecb4c-321">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-321">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="ecb4c-322">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span>
* <span data-ttu-id="ecb4c-323">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-323">This yields classes that are easier to test.</span></span> <span data-ttu-id="ecb4c-324">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="ecb4c-325">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="ecb4c-325">Design services for dependency injection</span></span> <span data-ttu-id="ecb4c-326">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-326">Best practices are to:</span></span>
* <span data-ttu-id="ecb4c-327">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-327">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="ecb4c-328">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ecb4c-329">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-329">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="ecb4c-330">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ecb4c-331">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-331">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ecb4c-332">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ecb4c-333">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ecb4c-334">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="ecb4c-335">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="ecb4c-336">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="ecb4c-337">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="ecb4c-337">Disposal of services</span></span>
* <span data-ttu-id="ecb4c-338">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="ecb4c-339">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="ecb4c-340">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ecb4c-341">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="ecb4c-341">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ecb4c-342">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-342">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="ecb4c-343">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-343">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="ecb4c-344">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-344">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="ecb4c-345">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="ecb4c-346">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="ecb4c-346">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="ecb4c-347">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-347">Transient, limited lifetime</span></span>

<span data-ttu-id="ecb4c-348">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-348">**Scenario**</span></span> <span data-ttu-id="ecb4c-349">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="ecb4c-350">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-350">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="ecb4c-351">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-351">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="ecb4c-352">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-352">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ecb4c-353">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-353">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="ecb4c-354">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="ecb4c-355">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-355">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="ecb4c-356">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="ecb4c-357">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="ecb4c-358">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-358">Shared Instance, limited lifetime</span></span> <span data-ttu-id="ecb4c-359">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-359">**Scenario**</span></span> <span data-ttu-id="ecb4c-360">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="ecb4c-361">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-361">**Solution**</span></span>

* <span data-ttu-id="ecb4c-362">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="ecb4c-363"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="ecb4c-364">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ecb4c-365">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-365">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="ecb4c-366">일반 지침</span><span class="sxs-lookup"><span data-stu-id="ecb4c-366">General Guidelines</span></span> <span data-ttu-id="ecb4c-367">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="ecb4c-368">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-368">Use the factory pattern instead.</span></span> <span data-ttu-id="ecb4c-369">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ecb4c-370">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="ecb4c-371">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ecb4c-372"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="ecb4c-373">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-373">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="ecb4c-374">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="ecb4c-375">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="ecb4c-375">Default service container replacement</span></span>
* <span data-ttu-id="ecb4c-376">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="ecb4c-377">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="ecb4c-378">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="ecb4c-378">Property injection</span></span>

<span data-ttu-id="ecb4c-379">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="ecb4c-379">Injection based on name</span></span>

* <span data-ttu-id="ecb4c-380">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="ecb4c-380">Child containers</span></span>
* <span data-ttu-id="ecb4c-381">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="ecb4c-381">Custom lifetime management</span></span>
* <span data-ttu-id="ecb4c-382">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="ecb4c-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ecb4c-383">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="ecb4c-383">Convention-based registration</span></span>
* <span data-ttu-id="ecb4c-384">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="ecb4c-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="ecb4c-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ecb4c-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ecb4c-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="ecb4c-387">유예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="ecb4c-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="ecb4c-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="ecb4c-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="ecb4c-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="ecb4c-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ecb4c-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="ecb4c-391">Unity</span><span class="sxs-lookup"><span data-stu-id="ecb4c-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="ecb4c-392">스레드 보안</span><span class="sxs-lookup"><span data-stu-id="ecb4c-392">Thread safety</span></span>

* <span data-ttu-id="ecb4c-393">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="ecb4c-394">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="ecb4c-395">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ecb4c-396">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="ecb4c-397">권장 사항</span><span class="sxs-lookup"><span data-stu-id="ecb4c-397">Recommendations</span></span> <span data-ttu-id="ecb4c-398">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ecb4c-399">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ecb4c-400">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-400">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="ecb4c-401">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="ecb4c-402">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

  <span data-ttu-id="ecb4c-403">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

  <span data-ttu-id="ecb4c-404">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-404">It's better to request the actual item via DI.</span></span>

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

* <span data-ttu-id="ecb4c-405">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span> <span data-ttu-id="ecb4c-406">‘서비스 로케이터 패턴’을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-406">Avoid using the *service locator pattern*.</span></span>

* <span data-ttu-id="ecb4c-407">예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService*>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

<span data-ttu-id="ecb4c-408">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-408">**Incorrect:**</span></span> <span data-ttu-id="ecb4c-409">**올바름**:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-409">**Correct**:</span></span>

<span data-ttu-id="ecb4c-410">피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="ecb4c-411">이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="ecb4c-412">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="ecb4c-413">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="ecb4c-414">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="ecb4c-415">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-415">DI is an *alternative* to static/global object access patterns.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ecb4c-416">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="ecb4c-417">DI의 다중 테넌트에 권장되는 패턴</span><span class="sxs-lookup"><span data-stu-id="ecb4c-417">Recommended patterns for multi-tenancy in DI</span></span>
* <span data-ttu-id="ecb4c-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 다중 테넌트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span>
* <span data-ttu-id="ecb4c-419">자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>
* <span data-ttu-id="ecb4c-420">CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 https://github.com/OrchardCMS/OrchardCore.Samples 에서 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>
* <span data-ttu-id="ecb4c-421">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-421">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[<span data-ttu-id="ecb4c-422">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="ecb4c-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[<span data-ttu-id="ecb4c-423">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="ecb4c-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)

[<span data-ttu-id="ecb4c-424">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="ecb4c-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)

## <a name="overview-of-dependency-injection"></a>[<span data-ttu-id="ecb4c-425">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="ecb4c-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)

<span data-ttu-id="ecb4c-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="ecb4c-426">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span> <span data-ttu-id="ecb4c-427">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="ecb4c-428">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="ecb4c-429">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ecb4c-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="ecb4c-430">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="ecb4c-430">Overview of dependency injection</span></span> <span data-ttu-id="ecb4c-431">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-431">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="ecb4c-432">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="ecb4c-433">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="ecb4c-434">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="ecb4c-435">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="ecb4c-436">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="ecb4c-437">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="ecb4c-438">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="ecb4c-439">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="ecb4c-440">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="ecb4c-441">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="ecb4c-442">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-442">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="ecb4c-443">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-443">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="ecb4c-444">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-444">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="ecb4c-445">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="ecb4c-446">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ecb4c-447">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="ecb4c-448">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="ecb4c-449">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="ecb4c-450">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="ecb4c-451">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="ecb4c-452">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="ecb4c-453">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-453">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="ecb4c-454">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="ecb4c-455">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="ecb4c-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="ecb4c-456">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="ecb4c-457">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="ecb4c-458">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="ecb4c-459">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="ecb4c-460">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="ecb4c-461">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="ecb4c-462">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="ecb4c-463">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="ecb4c-464">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="ecb4c-465">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-465">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="ecb4c-466">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="ecb4c-467">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="ecb4c-468">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="ecb4c-469">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-469">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ecb4c-470">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="ecb4c-471">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-471">Services injected into Startup</span></span> <span data-ttu-id="ecb4c-472">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="ecb4c-473">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-473">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="ecb4c-474">자세한 내용은 <xref:fundamentals/startup>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-474">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="ecb4c-475">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-475">Framework-provided services</span></span> | <span data-ttu-id="ecb4c-476">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-477">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="ecb4c-478">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="ecb4c-479">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-479">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="ecb4c-480">서비스 유형</span><span class="sxs-lookup"><span data-stu-id="ecb4c-480">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="ecb4c-481">수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-481">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="ecb4c-482">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-483">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="ecb4c-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="ecb4c-486">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="ecb4c-488">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-488">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="ecb4c-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="ecb4c-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="ecb4c-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-491">Singleton</span></span>

<span data-ttu-id="ecb4c-492">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-492">Transient</span></span> <span data-ttu-id="ecb4c-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-493">Singleton</span></span>

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

<span data-ttu-id="ecb4c-494">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-494">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="ecb4c-495">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-495">Singleton</span></span>

<span data-ttu-id="ecb4c-496">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="ecb4c-496">Register additional services with extension methods</span></span> <span data-ttu-id="ecb4c-497">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="ecb4c-498">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="ecb4c-499">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="ecb4c-500">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-500">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="ecb4c-501">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-501">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="ecb4c-502">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="ecb4c-503">임시</span><span class="sxs-lookup"><span data-stu-id="ecb4c-503">Transient</span></span> <span data-ttu-id="ecb4c-504">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="ecb4c-505">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="ecb4c-506">Scoped</span><span class="sxs-lookup"><span data-stu-id="ecb4c-506">Scoped</span></span>

<span data-ttu-id="ecb4c-507">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="ecb4c-508">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="ecb4c-509">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="ecb4c-510">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="ecb4c-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="ecb4c-511">Singleton</span></span> <span data-ttu-id="ecb4c-512">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="ecb4c-513">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-513">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="ecb4c-514">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="ecb4c-515">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="ecb4c-516">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-516">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="ecb4c-517">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="ecb4c-518">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="ecb4c-518">Service registration methods</span></span> | <span data-ttu-id="ecb4c-519">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="ecb4c-520">방법</span><span class="sxs-lookup"><span data-stu-id="ecb4c-520">Method</span></span> | <span data-ttu-id="ecb4c-521">자동</span><span class="sxs-lookup"><span data-stu-id="ecb4c-521">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="ecb4c-522">개체</span><span class="sxs-lookup"><span data-stu-id="ecb4c-522">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="ecb4c-523">삭제</span><span class="sxs-lookup"><span data-stu-id="ecb4c-523">disposal</span></span> | <span data-ttu-id="ecb4c-524">여러</span><span class="sxs-lookup"><span data-stu-id="ecb4c-524">Multiple</span></span> | <span data-ttu-id="ecb4c-525">구현</span><span class="sxs-lookup"><span data-stu-id="ecb4c-525">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-526">인수 전달</span><span class="sxs-lookup"><span data-stu-id="ecb4c-526">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="ecb4c-527">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-527">Example:</span></span> | <span data-ttu-id="ecb4c-528">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-528">Yes</span></span> | <span data-ttu-id="ecb4c-529">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="ecb4c-530">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-530">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="ecb4c-531">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-531">Examples:</span></span> | <span data-ttu-id="ecb4c-532">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-532">Yes</span></span> | <span data-ttu-id="ecb4c-533">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-533">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-534">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-534">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="ecb4c-535">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-535">Example:</span></span> | <span data-ttu-id="ecb4c-536">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-536">Yes</span></span> | <span data-ttu-id="ecb4c-537">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-537">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="ecb4c-538">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-538">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="ecb4c-539">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-539">Examples:</span></span> | <span data-ttu-id="ecb4c-540">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-540">No</span></span> | <span data-ttu-id="ecb4c-541">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-541">Yes</span></span> |

<span data-ttu-id="ecb4c-542">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-542">Yes</span></span> <span data-ttu-id="ecb4c-543">예제:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-543">Examples:</span></span>

<span data-ttu-id="ecb4c-544">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-544">No</span></span>

<span data-ttu-id="ecb4c-545">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-545">No</span></span> <span data-ttu-id="ecb4c-546">예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-546">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="ecb4c-547">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="ecb4c-548">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="ecb4c-549">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="ecb4c-550">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="ecb4c-551">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="ecb4c-552">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-552">For more information, see:</span></span> <span data-ttu-id="ecb4c-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="ecb4c-554">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="ecb4c-555">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="ecb4c-556">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="ecb4c-557">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="ecb4c-558">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-558">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="ecb4c-559">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="ecb4c-560">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="ecb4c-560">Constructor injection behavior</span></span>

<span data-ttu-id="ecb4c-561">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-561">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="ecb4c-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="ecb4c-563">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="ecb4c-564">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="ecb4c-565">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="ecb4c-566">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="ecb4c-567">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="ecb4c-568">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="ecb4c-568">Entity Framework contexts</span></span> <span data-ttu-id="ecb4c-569">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="ecb4c-570">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="ecb4c-571">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="ecb4c-572">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="ecb4c-572">Lifetime and registration options</span></span> <span data-ttu-id="ecb4c-573">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="ecb4c-574">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="ecb4c-575">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="ecb4c-576">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="ecb4c-577">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="ecb4c-578">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="ecb4c-579">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="ecb4c-580">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="ecb4c-581">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-581">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="ecb4c-582">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="ecb4c-583">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-583">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="ecb4c-584">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="ecb4c-585">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="ecb4c-586">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="ecb4c-587">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="ecb4c-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="ecb4c-588">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="ecb4c-589">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="ecb4c-590">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="ecb4c-591">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-591">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="ecb4c-592">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="ecb4c-592">**First request:**</span></span>

<span data-ttu-id="ecb4c-593">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-593">Controller operations:</span></span>

<span data-ttu-id="ecb4c-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="ecb4c-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="ecb4c-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ecb4c-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ecb4c-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-597">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ecb4c-598">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-598">`OperationService` operations:</span></span>

<span data-ttu-id="ecb4c-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="ecb4c-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="ecb4c-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="ecb4c-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="ecb4c-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-602">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="ecb4c-603">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-603">**Second request:**</span></span>

<span data-ttu-id="ecb4c-604">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-604">Controller operations:</span></span>

<span data-ttu-id="ecb4c-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="ecb4c-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="ecb4c-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ecb4c-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="ecb4c-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="ecb4c-608">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="ecb4c-609">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-609">`OperationService` operations:</span></span>

* <span data-ttu-id="ecb4c-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="ecb4c-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="ecb4c-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="ecb4c-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="ecb4c-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="ecb4c-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="ecb4c-613">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="ecb4c-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="ecb4c-614">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="ecb4c-615">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-615">*Transient* objects are always different.</span></span>

<span data-ttu-id="ecb4c-616">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="ecb4c-617">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-617">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="ecb4c-618">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ecb4c-619">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ecb4c-620">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="ecb4c-620">Call services from main</span></span>

* <span data-ttu-id="ecb4c-621">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="ecb4c-622">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="ecb4c-623">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="ecb4c-624">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="ecb4c-624">Scope validation</span></span>

<span data-ttu-id="ecb4c-625">앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="ecb4c-626">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="ecb4c-627">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ecb4c-628">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="ecb4c-629">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ecb4c-630">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-630">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="ecb4c-631">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ecb4c-632">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ecb4c-633">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="ecb4c-634">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-634">Request Services</span></span> <span data-ttu-id="ecb4c-635">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="ecb4c-636">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-636">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="ecb4c-637">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="ecb4c-638">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-638">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="ecb4c-639">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span>
* <span data-ttu-id="ecb4c-640">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-640">This yields classes that are easier to test.</span></span> <span data-ttu-id="ecb4c-641">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="ecb4c-642">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="ecb4c-642">Design services for dependency injection</span></span> <span data-ttu-id="ecb4c-643">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-643">Best practices are to:</span></span>
* <span data-ttu-id="ecb4c-644">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-644">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="ecb4c-645">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="ecb4c-646">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-646">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="ecb4c-647">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="ecb4c-648">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-648">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="ecb4c-649">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="ecb4c-650">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="ecb4c-651">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="ecb4c-652">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="ecb4c-653">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="ecb4c-654">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="ecb4c-654">Disposal of services</span></span>
* <span data-ttu-id="ecb4c-655">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="ecb4c-656">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="ecb4c-657">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="ecb4c-658">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="ecb4c-658">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="ecb4c-659">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-659">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="ecb4c-660">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-660">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="ecb4c-661">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-661">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="ecb4c-662">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="ecb4c-663">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="ecb4c-663">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="ecb4c-664">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-664">Transient, limited lifetime</span></span>

<span data-ttu-id="ecb4c-665">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-665">**Scenario**</span></span> <span data-ttu-id="ecb4c-666">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="ecb4c-667">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-667">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="ecb4c-668">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-668">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="ecb4c-669">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-669">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="ecb4c-670">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-670">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="ecb4c-671">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="ecb4c-672">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-672">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="ecb4c-673">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="ecb4c-674">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="ecb4c-675">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="ecb4c-675">Shared Instance, limited lifetime</span></span> <span data-ttu-id="ecb4c-676">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-676">**Scenario**</span></span> <span data-ttu-id="ecb4c-677">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="ecb4c-678">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-678">**Solution**</span></span>

* <span data-ttu-id="ecb4c-679">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="ecb4c-680"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="ecb4c-681">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="ecb4c-682">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-682">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="ecb4c-683">일반 지침</span><span class="sxs-lookup"><span data-stu-id="ecb4c-683">General Guidelines</span></span> <span data-ttu-id="ecb4c-684">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="ecb4c-685">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-685">Use the factory pattern instead.</span></span> <span data-ttu-id="ecb4c-686">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="ecb4c-687">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="ecb4c-688">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="ecb4c-689"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="ecb4c-690">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-690">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="ecb4c-691">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="ecb4c-692">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="ecb4c-692">Default service container replacement</span></span>
* <span data-ttu-id="ecb4c-693">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="ecb4c-694">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="ecb4c-695">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="ecb4c-695">Property injection</span></span>

<span data-ttu-id="ecb4c-696">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="ecb4c-696">Injection based on name</span></span>

* <span data-ttu-id="ecb4c-697">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="ecb4c-697">Child containers</span></span>
* <span data-ttu-id="ecb4c-698">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="ecb4c-698">Custom lifetime management</span></span>
* <span data-ttu-id="ecb4c-699">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="ecb4c-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="ecb4c-700">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="ecb4c-700">Convention-based registration</span></span>
* <span data-ttu-id="ecb4c-701">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="ecb4c-702">Autofac</span><span class="sxs-lookup"><span data-stu-id="ecb4c-702">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="ecb4c-703">DryIoc</span><span class="sxs-lookup"><span data-stu-id="ecb4c-703">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="ecb4c-704">유예</span><span class="sxs-lookup"><span data-stu-id="ecb4c-704">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="ecb4c-705">LightInject</span><span class="sxs-lookup"><span data-stu-id="ecb4c-705">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="ecb4c-706">Lamar</span><span class="sxs-lookup"><span data-stu-id="ecb4c-706">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="ecb4c-707">Stashbox</span><span class="sxs-lookup"><span data-stu-id="ecb4c-707">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="ecb4c-708">Unity</span><span class="sxs-lookup"><span data-stu-id="ecb4c-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="ecb4c-709">스레드 보안</span><span class="sxs-lookup"><span data-stu-id="ecb4c-709">Thread safety</span></span>

* <span data-ttu-id="ecb4c-710">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="ecb4c-711">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="ecb4c-712">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="ecb4c-713">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="ecb4c-714">권장 사항</span><span class="sxs-lookup"><span data-stu-id="ecb4c-714">Recommendations</span></span> <span data-ttu-id="ecb4c-715">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="ecb4c-716">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="ecb4c-717">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-717">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="ecb4c-718">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span>

  * <span data-ttu-id="ecb4c-719">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

    <span data-ttu-id="ecb4c-720">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

    <span data-ttu-id="ecb4c-721">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-721">It's better to request the actual item via DI.</span></span>

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

  * <span data-ttu-id="ecb4c-722">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="ecb4c-723">‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

<span data-ttu-id="ecb4c-724">DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span> <span data-ttu-id="ecb4c-725">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="ecb4c-725">**Incorrect:**</span></span>

<span data-ttu-id="ecb4c-726">**올바름**:</span><span class="sxs-lookup"><span data-stu-id="ecb4c-726">**Correct**:</span></span> <span data-ttu-id="ecb4c-727">런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ecb4c-728">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="ecb4c-729">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span>
* <span data-ttu-id="ecb4c-730">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>
* <span data-ttu-id="ecb4c-731">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-731">DI is an *alternative* to static/global object access patterns.</span></span>
* <span data-ttu-id="ecb4c-732">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ecb4c-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>
* <span data-ttu-id="ecb4c-733">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ecb4c-733">Additional resources</span></span>

::: moniker-end
