---
title: ASP.NET Core에서 종속성 주입
author: rick-anderson
description: ASP.NET Core에서 종속성 주입을 구현하는 방법 및 사용 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 2074aa75029cf27922b43545ec18c0cd8a50eb02
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793350"
---
# <a name="dependency-injection-in-aspnet-core"></a>ASP.NET Core에서 종속성 주입

작성자: [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.

MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>종속성 주입 개요

‘종속성’은 다른 개체에 필요한 모든 개체입니다. 앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.

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

`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다. `MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.

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

이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다. 이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.

* `MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.
* `MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다. 여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.
* 이 구현은 단위 테스트하기가 어렵습니다. 앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.

종속성 주입은 다음을 통해 이러한 문제를 해결합니다.

* 인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.
* 서비스 컨테이너에 종속성 등록. ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다. 서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.
* 서비스가 사용되는 클래스의 생성자에 주입됨. 프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.

[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다. 종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다. 요청된 각 종속성은 차례로 자체 종속성을 요청합니다. 컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다. 해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .

`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다. `IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다. `ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.

컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다. 이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다. [서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> 각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다. 예를 들어 `services.AddMvc()`는 [Razor Pages와 MVC에서 요청하는 서비스를 추가합니다. 앱에서 이 규칙을 따르는 것이 좋습니다. 확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.

서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.

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

서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다. 이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.

샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>시작에 삽입된 서비스

제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

서비스는 `Startup.Configure`에 삽입할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="framework-provided-services"></a>프레임워크에서 제공하는 서비스

`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다. 처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다. ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다. 다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.

| 서비스 종류 | 수명 |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>확장 메서드를 사용하여 추가 서비스 등록

서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다. 다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.

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

자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.

## <a name="service-lifetimes"></a>서비스 수명

등록된 각 서비스의 수명을 적절히 선택합니다. ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.

### <a name="transient"></a>Transient

Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다. 이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.

요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.

### <a name="scoped"></a>Scoped

Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.

요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.

> [!WARNING]
> 미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다. [생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요. 자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.

### <a name="singleton"></a>Singleton

싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다. 모든 후속 요청에서는 같은 인스턴스를 사용합니다. 앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다. 싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.

요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.

> [!WARNING]
> 범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다. 이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.

## <a name="service-registration-methods"></a>서비스 등록 메서드

서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.

| 메서드 | 자동<br>개체<br>삭제 | 여러<br>구현 | 인수 전달 |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>예:<br>`services.AddSingleton<IMyDep, MyDep>();` | 예 | 예 | 아니요 |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 예 | 예 | 예 |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>예:<br>`services.AddSingleton<MyDep>();` | 예 | 아니요 | 아니요 |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 아니요 | 예 | 예 |
| `AddSingleton(new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | 아니요 | 아니요 | 예 |

형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요. 여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.

`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.

다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다. 두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

자세한 내용은 다음을 참조하세요.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다. 여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다. 서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다. 일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.

다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다. 두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다. 세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>생성자 주입 동작

다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다. `ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.

생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.

`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.

`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다. 생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.

## <a name="entity-framework-contexts"></a>Entity Framework 컨텍스트

Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다. 데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다. 지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.

## <a name="lifetime-and-registration-options"></a>수명 및 등록 옵션

이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요. 다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

인터페이스들은 `Operation` 클래스에서 구현됩니다. `Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다. 종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.

* 컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다. `OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다. 새 인스턴스는 다른 `OperationId`를 생성합니다.
* 클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다. 전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.
* 싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다. 이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).

이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다. 샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다. 그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

다음 출력은 두 요청의 결과를 보여 줍니다.

**첫 번째 요청:** :

컨트롤러 작업:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

`OperationService` 작업:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

**두 번째 요청**:

컨트롤러 작업:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

`OperationService` 작업:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.

* *Transient* 개체는 항상 다릅니다. 첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다. 각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.
* *Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.
* *Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.

## <a name="call-services-from-main"></a>Main에서 서비스 호출

[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다. 이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다. 다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.

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

## <a name="scope-validation"></a>범위 유효성 검사

앱이 개발 환경에서 실행 중이고 호스트를 빌드하기 위해 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)를 호출하는 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.

* '직접으로' in current translation is awkward/inappropriate in terms of grammar.
* 범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.

루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다. 루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.

범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다. 범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다. 서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.

자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.

## <a name="request-services"></a>요청 서비스

`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.

요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다. 개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.

일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다. 대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 허용합니다. 이 방법이 테스트하기 쉬운 클래스를 생성합니다.

ASP.NET Core는 요청당 범위를 만들고 `RequestServices`는 범위가 지정된 서비스 공급자를 공개합니다. 범위가 지정된 모든 서비스는 요청이 활성 상태인 동안 유효합니다.

> [!NOTE]
> `RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.

## <a name="design-services-for-dependency-injection"></a>종속성 주입을 위한 서비스 디자인

모범 사례는 다음과 같습니다.

* 종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.
* 상태 저장 정적 클래스 및 멤버를 사용하지 마세요. 대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.
* 서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요. 직접 인스턴스화는 코드를 특정 구현에 결합합니다.
* 앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.

클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다. 해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요. [Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다. 비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.

### <a name="disposal-of-services"></a>서비스 삭제

컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다. 인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.

다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.

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

다음 예제에서는

* 서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.
* 의도된 서비스 수명을 프레임워크가 알지 못합니다.
* 프레임워크가 서비스를 자동으로 삭제하지 않습니다.
* 개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>임시 및 공유 인스턴스에 대한 IDisposable 지침

#### <a name="transient-limited-lifetime"></a>임시적인 제한 수명

**시나리오**

앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.

* 인스턴스는 루트 범위에서 확인됩니다.
* 범위가 끝나기 전에 인스턴스를 삭제해야 합니다.

**해결 방법**

부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다. 이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다. 최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.

* 해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.
* 해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.

#### <a name="shared-instance-limited-lifetime"></a>공유 인스턴스 및 제한 수명

**시나리오**

앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.

**해결 방법**

인스턴스를 범위가 지정된 수명으로 등록합니다. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다. 범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다. 수명을 종료해야 하는 경우 범위를 삭제합니다.

#### <a name="general-guidelines"></a>일반 지침

* 임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요. 대신 팩터리 패턴을 사용합니다.
* 루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요. 일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.
* DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다. <xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.
* 범위는 서비스의 수명을 제어하는 데 사용되어야 합니다. 범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.

## <a name="default-service-container-replacement"></a>기본 서비스 컨테이너 바꾸기

기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다. 기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.

* 속성 삽입
* 이름에 기반한 삽입
* 자식 컨테이너
* 사용자 지정 수명 관리
* 초기화 지연에 대한 `Func<T>` 지원
* 규칙 기반 등록

다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [유예](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>스레드로부터의 안전성

스레드로부터 안전한 싱글톤 서비스를 만듭니다. 싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.

[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다. 형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.

## <a name="recommendations"></a>권장 사항

* `async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다. C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.

* 데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요. 예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다. 구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다. 마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오. DI를 통해 실제 항목을 요청하는 것이 좋습니다.

* 서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.

* ‘서비스 로케이터 패턴’을 사용하지 마세요. 예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService*>를 호출하지 마세요.

  **잘못된 예:**

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

  **올바른 예**:

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

* 피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다. 이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.

* `HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.

모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다. 예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.

DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다. 고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>DI의 다중 테넌트에 권장되는 패턴

[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 다중 테넌트를 제공합니다. 자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.

CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 https://github.com/OrchardCMS/OrchardCore.Samples 에서 샘플 앱을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [IDisposables in ASP.NET Core를 삭제하는 네 가지 방법](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.

MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>종속성 주입 개요

‘종속성’은 다른 개체에 필요한 모든 개체입니다. 앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.

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

`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다. `MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.

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

이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다. 이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.

* `MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.
* `MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다. 여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.
* 이 구현은 단위 테스트하기가 어렵습니다. 앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.

종속성 주입은 다음을 통해 이러한 문제를 해결합니다.

* 인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.
* 서비스 컨테이너에 종속성 등록. ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다. 서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.
* 서비스가 사용되는 클래스의 생성자에 주입됨. 프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.

[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다. 종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다. 요청된 각 종속성은 차례로 자체 종속성을 요청합니다. 컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다. 해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .

`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다. `IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다. `ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.

컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다. 이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다. [서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> 각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다. 예를 들어 `services.AddMvc()`는 [Razor Pages와 MVC에서 요청하는 서비스를 추가합니다. 앱에서 이 규칙을 따르는 것이 좋습니다. 확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.

서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.

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

서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다. 이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.

샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>시작에 삽입된 서비스

제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

서비스는 `Startup.Configure`에 삽입할 수 있습니다.

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

자세한 내용은 <xref:fundamentals/startup>를 참조하세요.

## <a name="framework-provided-services"></a>프레임워크에서 제공하는 서비스

`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다. 처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다. ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다. 다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.

| 서비스 종류 | 수명 |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>확장 메서드를 사용하여 추가 서비스 등록

서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다. 다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.

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

자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.

## <a name="service-lifetimes"></a>서비스 수명

등록된 각 서비스의 수명을 적절히 선택합니다. ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.

### <a name="transient"></a>Transient

Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다. 이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.

요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.

### <a name="scoped"></a>Scoped

Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.

요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.

> [!WARNING]
> 미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다. [생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요. 자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.

### <a name="singleton"></a>Singleton

싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다. 모든 후속 요청에서는 같은 인스턴스를 사용합니다. 앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다. 싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.

요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.

> [!WARNING]
> 범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다. 이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.

## <a name="service-registration-methods"></a>서비스 등록 메서드

서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.

| 메서드 | 자동<br>개체<br>삭제 | 여러<br>구현 | 인수 전달 |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>예:<br>`services.AddSingleton<IMyDep, MyDep>();` | 예 | 예 | 아니요 |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 예 | 예 | 예 |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>예:<br>`services.AddSingleton<MyDep>();` | 예 | 아니요 | 아니요 |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 아니요 | 예 | 예 |
| `AddSingleton(new {IMPLEMENTATION})`<br>예:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | 아니요 | 아니요 | 예 |

형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요. 여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.

`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.

다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다. 두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

자세한 내용은 다음을 참조하세요.

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다. 여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다. 서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다. 일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.

다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다. 두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다. 세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>생성자 주입 동작

다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다. `ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.

생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.

`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.

`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다. 생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.

## <a name="entity-framework-contexts"></a>Entity Framework 컨텍스트

Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다. 데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다. 지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.

## <a name="lifetime-and-registration-options"></a>수명 및 등록 옵션

이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요. 다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

인터페이스들은 `Operation` 클래스에서 구현됩니다. `Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다. 종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.

* 컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다. `OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다. 새 인스턴스는 다른 `OperationId`를 생성합니다.
* 클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다. 전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.
* 싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다. 이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).

이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다. 샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다. 그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

다음 출력은 두 요청의 결과를 보여 줍니다.

**첫 번째 요청:** :

컨트롤러 작업:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

`OperationService` 작업:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

**두 번째 요청**:

컨트롤러 작업:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

`OperationService` 작업:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
인스턴스: 00000000-0000-0000-0000-000000000000

어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.

* *Transient* 개체는 항상 다릅니다. 첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다. 각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.
* *Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.
* *Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.

## <a name="call-services-from-main"></a>Main에서 서비스 호출

[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다. 이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다. 다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.

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

## <a name="scope-validation"></a>범위 유효성 검사

앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.

* '직접으로' in current translation is awkward/inappropriate in terms of grammar.
* 범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.

루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다. 루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.

범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다. 범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다. 서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.

자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.

## <a name="request-services"></a>요청 서비스

`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.

요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다. 개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.

일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다. 대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다. 이 방법이 테스트하기 쉬운 클래스를 생성합니다.

> [!NOTE]
> `RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.

## <a name="design-services-for-dependency-injection"></a>종속성 주입을 위한 서비스 디자인

모범 사례는 다음과 같습니다.

* 종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.
* 상태 저장 정적 클래스 및 멤버를 사용하지 마세요. 대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.
* 서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요. 직접 인스턴스화는 코드를 특정 구현에 결합합니다.
* 앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.

클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다. 해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요. [Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다. 비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.

### <a name="disposal-of-services"></a>서비스 삭제

컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다. 인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.

다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.

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

다음 예제에서는

* 서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.
* 의도된 서비스 수명을 프레임워크가 알지 못합니다.
* 프레임워크가 서비스를 자동으로 삭제하지 않습니다.
* 개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>임시 및 공유 인스턴스에 대한 IDisposable 지침

#### <a name="transient-limited-lifetime"></a>임시적인 제한 수명

**시나리오**

앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.

* 인스턴스는 루트 범위에서 확인됩니다.
* 범위가 끝나기 전에 인스턴스를 삭제해야 합니다.

**해결 방법**

부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다. 이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다. 최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.

* 해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.
* 해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.

#### <a name="shared-instance-limited-lifetime"></a>공유 인스턴스 및 제한 수명

**시나리오**

앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.

**해결 방법**

인스턴스를 범위가 지정된 수명으로 등록합니다. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다. 범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다. 수명을 종료해야 하는 경우 범위를 삭제합니다.

#### <a name="general-guidelines"></a>일반 지침

* 임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요. 대신 팩터리 패턴을 사용합니다.
* 루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요. 일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.
* DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다. <xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.
* 범위는 서비스의 수명을 제어하는 데 사용되어야 합니다. 범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.

## <a name="default-service-container-replacement"></a>기본 서비스 컨테이너 바꾸기

기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다. 기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.

* 속성 삽입
* 이름에 기반한 삽입
* 자식 컨테이너
* 사용자 지정 수명 관리
* 초기화 지연에 대한 `Func<T>` 지원
* 규칙 기반 등록

다음 타사 컨테이너는 ASP.NET Core 앱에서 사용할 수 있습니다.

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [유예](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>스레드로부터의 안전성

스레드로부터 안전한 싱글톤 서비스를 만듭니다. 싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.

[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다. 형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.

## <a name="recommendations"></a>권장 사항

* `async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다. C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.

* 데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요. 예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다. 구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다. 마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오. DI를 통해 실제 항목을 요청하는 것이 좋습니다.

* 서비스에 정적으로 액세스(예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력)하지 마세요.

* ‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.

  * DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.

    **잘못된 예:**

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

    **올바른 예**:

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

  * 런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.

* `HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.

모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다. 예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.

DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다. 고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [IDisposables in ASP.NET Core를 삭제하는 네 가지 방법](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)

::: moniker-end
