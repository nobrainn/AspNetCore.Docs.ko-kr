---
title: ASP.NET Core에서 종속성 주입
author: rick-anderson
description: ASP.NET Core에서 종속성 주입을 구현하는 방법 및 사용 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 34ed08a5b49b56fd37628032ac73fe03a34448e6
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240851"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="be3e7-103">ASP.NET Core에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="be3e7-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="be3e7-104">작성자: [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="be3e7-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be3e7-105">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="be3e7-106">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="be3e7-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be3e7-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="be3e7-108">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="be3e7-108">Overview of dependency injection</span></span>

<span data-ttu-id="be3e7-109">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="be3e7-110">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="be3e7-111">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="be3e7-112">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="be3e7-113">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="be3e7-114">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="be3e7-115">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="be3e7-116">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="be3e7-117">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="be3e7-118">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="be3e7-119">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="be3e7-120">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="be3e7-121">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="be3e7-122">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="be3e7-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="be3e7-123">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="be3e7-124">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="be3e7-125">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="be3e7-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="be3e7-126">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="be3e7-127">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="be3e7-128">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="be3e7-129">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="be3e7-130">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="be3e7-131">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="be3e7-132">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="be3e7-133">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="be3e7-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="be3e7-134">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="be3e7-135">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="be3e7-136">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="be3e7-137">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="be3e7-138">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="be3e7-139">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="be3e7-140">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="be3e7-141">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="be3e7-142">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="be3e7-143">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="be3e7-144">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="be3e7-145">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="be3e7-146">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="be3e7-147">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="be3e7-148">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="be3e7-149">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-149">Services injected into Startup</span></span>

<span data-ttu-id="be3e7-150">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="be3e7-151">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="be3e7-152">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="be3e7-153">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-153">Framework-provided services</span></span>

<span data-ttu-id="be3e7-154">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="be3e7-155">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="be3e7-156">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="be3e7-157">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="be3e7-158">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="be3e7-158">Service Type</span></span> | <span data-ttu-id="be3e7-159">수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-160">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="be3e7-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="be3e7-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="be3e7-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="be3e7-164">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="be3e7-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-166">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="be3e7-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="be3e7-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="be3e7-170">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="be3e7-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="be3e7-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="be3e7-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="be3e7-174">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="be3e7-174">Register additional services with extension methods</span></span>

<span data-ttu-id="be3e7-175">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="be3e7-176">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="be3e7-177">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="be3e7-178">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-178">Service lifetimes</span></span>

<span data-ttu-id="be3e7-179">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="be3e7-180">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="be3e7-181">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-181">Transient</span></span>

<span data-ttu-id="be3e7-182">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="be3e7-183">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="be3e7-184">요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="be3e7-185">Scoped</span><span class="sxs-lookup"><span data-stu-id="be3e7-185">Scoped</span></span>

<span data-ttu-id="be3e7-186">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="be3e7-187">요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="be3e7-188">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="be3e7-189">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="be3e7-190">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="be3e7-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-191">Singleton</span></span>

<span data-ttu-id="be3e7-192">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="be3e7-193">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="be3e7-194">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="be3e7-195">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="be3e7-196">요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="be3e7-197">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="be3e7-198">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="be3e7-199">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="be3e7-199">Service registration methods</span></span>

<span data-ttu-id="be3e7-200">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="be3e7-201">메서드</span><span class="sxs-lookup"><span data-stu-id="be3e7-201">Method</span></span> | <span data-ttu-id="be3e7-202">자동</span><span class="sxs-lookup"><span data-stu-id="be3e7-202">Automatic</span></span><br><span data-ttu-id="be3e7-203">개체</span><span class="sxs-lookup"><span data-stu-id="be3e7-203">object</span></span><br><span data-ttu-id="be3e7-204">삭제</span><span class="sxs-lookup"><span data-stu-id="be3e7-204">disposal</span></span> | <span data-ttu-id="be3e7-205">여러</span><span class="sxs-lookup"><span data-stu-id="be3e7-205">Multiple</span></span><br><span data-ttu-id="be3e7-206">구현</span><span class="sxs-lookup"><span data-stu-id="be3e7-206">implementations</span></span> | <span data-ttu-id="be3e7-207">인수 전달</span><span class="sxs-lookup"><span data-stu-id="be3e7-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="be3e7-208">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="be3e7-209">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-209">Yes</span></span> | <span data-ttu-id="be3e7-210">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-210">Yes</span></span> | <span data-ttu-id="be3e7-211">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-212">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="be3e7-213">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-213">Yes</span></span> | <span data-ttu-id="be3e7-214">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-214">Yes</span></span> | <span data-ttu-id="be3e7-215">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="be3e7-216">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="be3e7-217">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-217">Yes</span></span> | <span data-ttu-id="be3e7-218">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-218">No</span></span> | <span data-ttu-id="be3e7-219">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-220">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="be3e7-221">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-221">No</span></span> | <span data-ttu-id="be3e7-222">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-222">Yes</span></span> | <span data-ttu-id="be3e7-223">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-224">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="be3e7-225">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-225">No</span></span> | <span data-ttu-id="be3e7-226">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-226">No</span></span> | <span data-ttu-id="be3e7-227">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-227">Yes</span></span> |

<span data-ttu-id="be3e7-228">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="be3e7-229">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="be3e7-230">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="be3e7-231">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="be3e7-232">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="be3e7-233">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="be3e7-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="be3e7-235">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="be3e7-236">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="be3e7-237">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="be3e7-238">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="be3e7-239">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="be3e7-240">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="be3e7-241">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="be3e7-241">Constructor injection behavior</span></span>

<span data-ttu-id="be3e7-242">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="be3e7-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="be3e7-244">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="be3e7-245">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="be3e7-246">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="be3e7-247">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="be3e7-248">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="be3e7-249">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="be3e7-249">Entity Framework contexts</span></span>

<span data-ttu-id="be3e7-250">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="be3e7-251">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="be3e7-252">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="be3e7-253">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="be3e7-253">Lifetime and registration options</span></span>

<span data-ttu-id="be3e7-254">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="be3e7-255">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="be3e7-256">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="be3e7-257">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="be3e7-258">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="be3e7-259">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="be3e7-260">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="be3e7-261">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="be3e7-262">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="be3e7-263">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="be3e7-264">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="be3e7-265">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="be3e7-266">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="be3e7-267">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="be3e7-268">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="be3e7-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="be3e7-269">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="be3e7-270">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="be3e7-271">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="be3e7-272">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="be3e7-273">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="be3e7-273">**First request:**</span></span>

<span data-ttu-id="be3e7-274">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-274">Controller operations:</span></span>

<span data-ttu-id="be3e7-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="be3e7-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="be3e7-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be3e7-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be3e7-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-278">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-279">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-279">`OperationService` operations:</span></span>

<span data-ttu-id="be3e7-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="be3e7-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="be3e7-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be3e7-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be3e7-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-283">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-284">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="be3e7-284">**Second request:**</span></span>

<span data-ttu-id="be3e7-285">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-285">Controller operations:</span></span>

<span data-ttu-id="be3e7-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="be3e7-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="be3e7-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be3e7-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be3e7-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-289">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-290">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-290">`OperationService` operations:</span></span>

<span data-ttu-id="be3e7-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="be3e7-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="be3e7-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be3e7-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be3e7-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-294">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-295">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="be3e7-296">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-296">*Transient* objects are always different.</span></span> <span data-ttu-id="be3e7-297">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="be3e7-298">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="be3e7-299">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="be3e7-300">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="be3e7-301">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="be3e7-301">Call services from main</span></span>

<span data-ttu-id="be3e7-302">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="be3e7-303">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="be3e7-304">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="be3e7-305">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="be3e7-305">Scope validation</span></span>

<span data-ttu-id="be3e7-306">앱이 개발 환경에서 실행 중이고 호스트를 빌드하기 위해 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)를 호출하는 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="be3e7-307">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="be3e7-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="be3e7-308">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="be3e7-309">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="be3e7-310">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="be3e7-311">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="be3e7-312">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="be3e7-313">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="be3e7-314">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="be3e7-315">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-315">Request Services</span></span>

<span data-ttu-id="be3e7-316">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="be3e7-317">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="be3e7-318">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="be3e7-319">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="be3e7-320">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="be3e7-321">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="be3e7-322">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="be3e7-323">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="be3e7-323">Design services for dependency injection</span></span>

<span data-ttu-id="be3e7-324">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-324">Best practices are to:</span></span>

* <span data-ttu-id="be3e7-325">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="be3e7-326">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="be3e7-327">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="be3e7-328">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="be3e7-329">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="be3e7-330">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="be3e7-331">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="be3e7-332">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="be3e7-333">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="be3e7-334">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="be3e7-335">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="be3e7-335">Disposal of services</span></span>

<span data-ttu-id="be3e7-336">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="be3e7-337">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="be3e7-338">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="be3e7-339">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="be3e7-339">In the following example:</span></span>

* <span data-ttu-id="be3e7-340">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="be3e7-341">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="be3e7-342">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="be3e7-343">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="be3e7-344">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="be3e7-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="be3e7-345">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-345">Transient, limited lifetime</span></span>

<span data-ttu-id="be3e7-346">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="be3e7-346">**Scenario**</span></span>

<span data-ttu-id="be3e7-347">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="be3e7-348">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="be3e7-349">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="be3e7-350">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="be3e7-350">**Solution**</span></span>

<span data-ttu-id="be3e7-351">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="be3e7-352">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="be3e7-353">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="be3e7-354">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="be3e7-355">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="be3e7-356">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="be3e7-357">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="be3e7-357">**Scenario**</span></span>

<span data-ttu-id="be3e7-358">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="be3e7-359">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="be3e7-359">**Solution**</span></span>

<span data-ttu-id="be3e7-360">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="be3e7-361"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="be3e7-362">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="be3e7-363">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="be3e7-364">일반 지침</span><span class="sxs-lookup"><span data-stu-id="be3e7-364">General Guidelines</span></span>

* <span data-ttu-id="be3e7-365">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="be3e7-366">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="be3e7-367">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="be3e7-368">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="be3e7-369">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="be3e7-370"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="be3e7-371">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="be3e7-372">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="be3e7-373">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="be3e7-373">Default service container replacement</span></span>

<span data-ttu-id="be3e7-374">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="be3e7-375">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="be3e7-376">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="be3e7-376">Property injection</span></span>
* <span data-ttu-id="be3e7-377">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="be3e7-377">Injection based on name</span></span>
* <span data-ttu-id="be3e7-378">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="be3e7-378">Child containers</span></span>
* <span data-ttu-id="be3e7-379">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="be3e7-379">Custom lifetime management</span></span>
* <span data-ttu-id="be3e7-380">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="be3e7-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="be3e7-381">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="be3e7-381">Convention-based registration</span></span>

<span data-ttu-id="be3e7-382">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="be3e7-383">Autofac</span><span class="sxs-lookup"><span data-stu-id="be3e7-383">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="be3e7-384">DryIoc</span><span class="sxs-lookup"><span data-stu-id="be3e7-384">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="be3e7-385">유예</span><span class="sxs-lookup"><span data-stu-id="be3e7-385">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="be3e7-386">LightInject</span><span class="sxs-lookup"><span data-stu-id="be3e7-386">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="be3e7-387">Lamar</span><span class="sxs-lookup"><span data-stu-id="be3e7-387">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="be3e7-388">Stashbox</span><span class="sxs-lookup"><span data-stu-id="be3e7-388">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="be3e7-389">Unity</span><span class="sxs-lookup"><span data-stu-id="be3e7-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="be3e7-390">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="be3e7-390">Thread safety</span></span>

<span data-ttu-id="be3e7-391">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="be3e7-392">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="be3e7-393">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="be3e7-394">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="be3e7-395">권장 사항</span><span class="sxs-lookup"><span data-stu-id="be3e7-395">Recommendations</span></span>

* <span data-ttu-id="be3e7-396">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="be3e7-397">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="be3e7-398">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="be3e7-399">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="be3e7-400">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="be3e7-401">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="be3e7-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="be3e7-402">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="be3e7-403">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="be3e7-404">‘서비스 로케이터 패턴’을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="be3e7-405">예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService*>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="be3e7-406">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="be3e7-406">**Incorrect:**</span></span>

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

  <span data-ttu-id="be3e7-407">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="be3e7-407">**Correct**:</span></span>

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

* <span data-ttu-id="be3e7-408">피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="be3e7-409">이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="be3e7-410">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="be3e7-411">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="be3e7-412">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="be3e7-413">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="be3e7-414">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="be3e7-415">DI의 다중 테넌트에 권장되는 패턴</span><span class="sxs-lookup"><span data-stu-id="be3e7-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="be3e7-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 다중 테넌트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="be3e7-417">자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="be3e7-418">CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 https://github.com/OrchardCMS/OrchardCore.Samples 에서 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be3e7-419">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be3e7-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="be3e7-420">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="be3e7-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="be3e7-421">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="be3e7-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="be3e7-422">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="be3e7-422">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="be3e7-423">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="be3e7-423">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="be3e7-424">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="be3e7-424">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be3e7-425">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="be3e7-426">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="be3e7-427">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="be3e7-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="be3e7-428">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="be3e7-428">Overview of dependency injection</span></span>

<span data-ttu-id="be3e7-429">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="be3e7-430">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="be3e7-431">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="be3e7-432">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="be3e7-433">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="be3e7-434">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="be3e7-435">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="be3e7-436">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="be3e7-437">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="be3e7-438">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="be3e7-439">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="be3e7-440">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="be3e7-441">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="be3e7-442">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="be3e7-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="be3e7-443">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="be3e7-444">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="be3e7-445">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="be3e7-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="be3e7-446">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="be3e7-447">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="be3e7-448">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="be3e7-449">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="be3e7-450">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="be3e7-451">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="be3e7-452">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="be3e7-453">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="be3e7-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="be3e7-454">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="be3e7-455">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="be3e7-456">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="be3e7-457">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="be3e7-458">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="be3e7-459">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="be3e7-460">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="be3e7-461">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="be3e7-462">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="be3e7-463">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="be3e7-464">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="be3e7-465">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="be3e7-466">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="be3e7-467">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="be3e7-468">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="be3e7-469">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-469">Services injected into Startup</span></span>

<span data-ttu-id="be3e7-470">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="be3e7-471">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="be3e7-472">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="be3e7-473">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-473">Framework-provided services</span></span>

<span data-ttu-id="be3e7-474">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="be3e7-475">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="be3e7-476">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="be3e7-477">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="be3e7-478">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="be3e7-478">Service Type</span></span> | <span data-ttu-id="be3e7-479">수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-480">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="be3e7-481">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="be3e7-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="be3e7-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="be3e7-484">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="be3e7-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-486">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="be3e7-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="be3e7-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="be3e7-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="be3e7-490">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="be3e7-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="be3e7-492">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="be3e7-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="be3e7-494">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="be3e7-494">Register additional services with extension methods</span></span>

<span data-ttu-id="be3e7-495">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="be3e7-496">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="be3e7-497">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="be3e7-498">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-498">Service lifetimes</span></span>

<span data-ttu-id="be3e7-499">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="be3e7-500">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="be3e7-501">Transient</span><span class="sxs-lookup"><span data-stu-id="be3e7-501">Transient</span></span>

<span data-ttu-id="be3e7-502">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="be3e7-503">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="be3e7-504">요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="be3e7-505">Scoped</span><span class="sxs-lookup"><span data-stu-id="be3e7-505">Scoped</span></span>

<span data-ttu-id="be3e7-506">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="be3e7-507">요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="be3e7-508">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="be3e7-509">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="be3e7-510">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="be3e7-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="be3e7-511">Singleton</span></span>

<span data-ttu-id="be3e7-512">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="be3e7-513">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="be3e7-514">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="be3e7-515">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="be3e7-516">요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="be3e7-517">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="be3e7-518">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="be3e7-519">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="be3e7-519">Service registration methods</span></span>

<span data-ttu-id="be3e7-520">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="be3e7-521">메서드</span><span class="sxs-lookup"><span data-stu-id="be3e7-521">Method</span></span> | <span data-ttu-id="be3e7-522">자동</span><span class="sxs-lookup"><span data-stu-id="be3e7-522">Automatic</span></span><br><span data-ttu-id="be3e7-523">개체</span><span class="sxs-lookup"><span data-stu-id="be3e7-523">object</span></span><br><span data-ttu-id="be3e7-524">삭제</span><span class="sxs-lookup"><span data-stu-id="be3e7-524">disposal</span></span> | <span data-ttu-id="be3e7-525">여러</span><span class="sxs-lookup"><span data-stu-id="be3e7-525">Multiple</span></span><br><span data-ttu-id="be3e7-526">구현</span><span class="sxs-lookup"><span data-stu-id="be3e7-526">implementations</span></span> | <span data-ttu-id="be3e7-527">인수 전달</span><span class="sxs-lookup"><span data-stu-id="be3e7-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="be3e7-528">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="be3e7-529">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-529">Yes</span></span> | <span data-ttu-id="be3e7-530">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-530">Yes</span></span> | <span data-ttu-id="be3e7-531">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-532">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="be3e7-533">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-533">Yes</span></span> | <span data-ttu-id="be3e7-534">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-534">Yes</span></span> | <span data-ttu-id="be3e7-535">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="be3e7-536">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="be3e7-537">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-537">Yes</span></span> | <span data-ttu-id="be3e7-538">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-538">No</span></span> | <span data-ttu-id="be3e7-539">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-540">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="be3e7-541">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-541">No</span></span> | <span data-ttu-id="be3e7-542">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-542">Yes</span></span> | <span data-ttu-id="be3e7-543">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="be3e7-544">예:</span><span class="sxs-lookup"><span data-stu-id="be3e7-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="be3e7-545">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-545">No</span></span> | <span data-ttu-id="be3e7-546">아니요</span><span class="sxs-lookup"><span data-stu-id="be3e7-546">No</span></span> | <span data-ttu-id="be3e7-547">예</span><span class="sxs-lookup"><span data-stu-id="be3e7-547">Yes</span></span> |

<span data-ttu-id="be3e7-548">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="be3e7-549">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="be3e7-550">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="be3e7-551">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="be3e7-552">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="be3e7-553">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="be3e7-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="be3e7-555">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="be3e7-556">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="be3e7-557">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="be3e7-558">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="be3e7-559">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="be3e7-560">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="be3e7-561">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="be3e7-561">Constructor injection behavior</span></span>

<span data-ttu-id="be3e7-562">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="be3e7-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="be3e7-564">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="be3e7-565">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="be3e7-566">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="be3e7-567">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="be3e7-568">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="be3e7-569">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="be3e7-569">Entity Framework contexts</span></span>

<span data-ttu-id="be3e7-570">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="be3e7-571">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="be3e7-572">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="be3e7-573">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="be3e7-573">Lifetime and registration options</span></span>

<span data-ttu-id="be3e7-574">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="be3e7-575">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="be3e7-576">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="be3e7-577">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="be3e7-578">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="be3e7-579">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="be3e7-580">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="be3e7-581">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="be3e7-582">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="be3e7-583">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="be3e7-584">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="be3e7-585">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="be3e7-586">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="be3e7-587">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="be3e7-588">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="be3e7-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="be3e7-589">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="be3e7-590">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="be3e7-591">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="be3e7-592">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="be3e7-593">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="be3e7-593">**First request:**</span></span>

<span data-ttu-id="be3e7-594">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-594">Controller operations:</span></span>

<span data-ttu-id="be3e7-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="be3e7-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="be3e7-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be3e7-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be3e7-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-598">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-599">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-599">`OperationService` operations:</span></span>

<span data-ttu-id="be3e7-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="be3e7-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="be3e7-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="be3e7-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="be3e7-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-603">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-604">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="be3e7-604">**Second request:**</span></span>

<span data-ttu-id="be3e7-605">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-605">Controller operations:</span></span>

<span data-ttu-id="be3e7-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="be3e7-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="be3e7-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be3e7-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be3e7-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-609">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-610">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="be3e7-610">`OperationService` operations:</span></span>

<span data-ttu-id="be3e7-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="be3e7-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="be3e7-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="be3e7-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="be3e7-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="be3e7-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="be3e7-614">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="be3e7-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="be3e7-615">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="be3e7-616">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-616">*Transient* objects are always different.</span></span> <span data-ttu-id="be3e7-617">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="be3e7-618">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="be3e7-619">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="be3e7-620">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="be3e7-621">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="be3e7-621">Call services from main</span></span>

<span data-ttu-id="be3e7-622">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="be3e7-623">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="be3e7-624">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="be3e7-625">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="be3e7-625">Scope validation</span></span>

<span data-ttu-id="be3e7-626">앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="be3e7-627">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="be3e7-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="be3e7-628">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="be3e7-629">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="be3e7-630">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="be3e7-631">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="be3e7-632">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="be3e7-633">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="be3e7-634">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="be3e7-635">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="be3e7-635">Request Services</span></span>

<span data-ttu-id="be3e7-636">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="be3e7-637">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="be3e7-638">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="be3e7-639">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="be3e7-640">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="be3e7-641">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="be3e7-642">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="be3e7-643">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="be3e7-643">Design services for dependency injection</span></span>

<span data-ttu-id="be3e7-644">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-644">Best practices are to:</span></span>

* <span data-ttu-id="be3e7-645">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="be3e7-646">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="be3e7-647">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="be3e7-648">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="be3e7-649">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="be3e7-650">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="be3e7-651">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="be3e7-652">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="be3e7-653">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="be3e7-654">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="be3e7-655">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="be3e7-655">Disposal of services</span></span>

<span data-ttu-id="be3e7-656">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="be3e7-657">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="be3e7-658">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="be3e7-659">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="be3e7-659">In the following example:</span></span>

* <span data-ttu-id="be3e7-660">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="be3e7-661">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="be3e7-662">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="be3e7-663">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="be3e7-664">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="be3e7-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="be3e7-665">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-665">Transient, limited lifetime</span></span>

<span data-ttu-id="be3e7-666">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="be3e7-666">**Scenario**</span></span>

<span data-ttu-id="be3e7-667">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="be3e7-668">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="be3e7-669">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="be3e7-670">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="be3e7-670">**Solution**</span></span>

<span data-ttu-id="be3e7-671">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="be3e7-672">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="be3e7-673">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="be3e7-674">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="be3e7-675">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="be3e7-676">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="be3e7-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="be3e7-677">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="be3e7-677">**Scenario**</span></span>

<span data-ttu-id="be3e7-678">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="be3e7-679">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="be3e7-679">**Solution**</span></span>

<span data-ttu-id="be3e7-680">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="be3e7-681"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="be3e7-682">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="be3e7-683">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="be3e7-684">일반 지침</span><span class="sxs-lookup"><span data-stu-id="be3e7-684">General Guidelines</span></span>

* <span data-ttu-id="be3e7-685">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="be3e7-686">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="be3e7-687">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="be3e7-688">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="be3e7-689">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="be3e7-690"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="be3e7-691">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="be3e7-692">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="be3e7-693">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="be3e7-693">Default service container replacement</span></span>

<span data-ttu-id="be3e7-694">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="be3e7-695">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="be3e7-696">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="be3e7-696">Property injection</span></span>
* <span data-ttu-id="be3e7-697">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="be3e7-697">Injection based on name</span></span>
* <span data-ttu-id="be3e7-698">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="be3e7-698">Child containers</span></span>
* <span data-ttu-id="be3e7-699">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="be3e7-699">Custom lifetime management</span></span>
* <span data-ttu-id="be3e7-700">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="be3e7-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="be3e7-701">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="be3e7-701">Convention-based registration</span></span>

<span data-ttu-id="be3e7-702">다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="be3e7-703">Autofac</span><span class="sxs-lookup"><span data-stu-id="be3e7-703">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="be3e7-704">DryIoc</span><span class="sxs-lookup"><span data-stu-id="be3e7-704">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="be3e7-705">유예</span><span class="sxs-lookup"><span data-stu-id="be3e7-705">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="be3e7-706">LightInject</span><span class="sxs-lookup"><span data-stu-id="be3e7-706">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="be3e7-707">Lamar</span><span class="sxs-lookup"><span data-stu-id="be3e7-707">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="be3e7-708">Stashbox</span><span class="sxs-lookup"><span data-stu-id="be3e7-708">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="be3e7-709">Unity</span><span class="sxs-lookup"><span data-stu-id="be3e7-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="be3e7-710">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="be3e7-710">Thread safety</span></span>

<span data-ttu-id="be3e7-711">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="be3e7-712">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="be3e7-713">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="be3e7-714">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="be3e7-715">권장 사항</span><span class="sxs-lookup"><span data-stu-id="be3e7-715">Recommendations</span></span>

* <span data-ttu-id="be3e7-716">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="be3e7-717">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="be3e7-718">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="be3e7-719">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="be3e7-720">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="be3e7-721">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="be3e7-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="be3e7-722">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="be3e7-723">서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="be3e7-724">‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="be3e7-725">DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="be3e7-726">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="be3e7-726">**Incorrect:**</span></span>

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

    <span data-ttu-id="be3e7-727">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="be3e7-727">**Correct**:</span></span>

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

  * <span data-ttu-id="be3e7-728">런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="be3e7-729">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="be3e7-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="be3e7-730">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="be3e7-731">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="be3e7-732">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="be3e7-733">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be3e7-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be3e7-734">추가 자료</span><span class="sxs-lookup"><span data-stu-id="be3e7-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="be3e7-735">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="be3e7-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="be3e7-736">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="be3e7-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="be3e7-737">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="be3e7-737">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="be3e7-738">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="be3e7-738">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="be3e7-739">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="be3e7-739">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
