---
title: .NET Core 및 ASP.NET Core의 로깅
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet 패키지에서 제공하는 로깅 프레임워크 사용법을 알아보세요.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110917"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="06cbd-103">.NET Core 및 ASP.NET Core의 로깅</span><span class="sxs-lookup"><span data-stu-id="06cbd-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="06cbd-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06cbd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06cbd-105">.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="06cbd-106">이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="06cbd-107">이 문서에 사용된 대부분의 코드 예제는 ASP.NET Core 앱에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="06cbd-108">해당 코드 조각의 로깅 관련 부분은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 모든 .NET Core 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="06cbd-109">비 웹 콘솔 앱에서 일반 호스트를 사용하는 방법에 대한 사례는 [백그라운드 작업 샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples)(<xref:fundamentals/host/hosted-services>)의 *Program.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="06cbd-110">제네릭 호스트를 사용하지 않는 앱의 로깅 코드는 [공급자 추가](#add-providers) 및 [로거 생성](#create-logs) 방식에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="06cbd-111">비 호스트 코드 예제는 문서의 해당 섹션에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="06cbd-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06cbd-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="06cbd-113">공급자 추가</span><span class="sxs-lookup"><span data-stu-id="06cbd-113">Add providers</span></span>

<span data-ttu-id="06cbd-114">로깅 공급자는 로그를 표시하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="06cbd-115">예를 들어 콘솔 공급자는 콘솔에 로그를 표시하고 Azure Application Insights 공급자는 로그를 Azure Application Insights에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="06cbd-116">여러 공급자를 추가하여 로그를 여러 대상으로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="06cbd-117">제네릭 호스트를 사용하는 앱에서 공급자를 추가하려면 *Program.cs*에서 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="06cbd-118">비 호스트 콘솔 앱에서는 `LoggerFactory`를 만드는 동안 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="06cbd-119">`LoggerFactory` 및 `AddConsole`에는 `Microsoft.Extensions.Logging`에 대한 `using` 문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="06cbd-120">기본 ASP.NET Core 프로젝트 템플릿은 다음과 같은 로깅 공급자를 추가하는 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="06cbd-121">콘솔</span><span class="sxs-lookup"><span data-stu-id="06cbd-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="06cbd-122">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="06cbd-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="06cbd-124">[EventLog](#windows-eventlog-provider)(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="06cbd-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="06cbd-125">이 기본 공급자들을 자신이 선택한 공급자로 대체할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="06cbd-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하고 원하는 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="06cbd-127">문서의 뒷부분에 나오는 [기본 제공 로깅 공급자](#built-in-logging-providers) 및 [타사 로깅 공급자](#third-party-logging-providers)에서 더 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="06cbd-128">로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-128">Create logs</span></span>

<span data-ttu-id="06cbd-129">로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="06cbd-130">웹앱 또는 호스트되는 서비스에서는 DI(종속성 주입)로부터 `ILogger`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="06cbd-131">비 호스트 콘솔 앱에서는 `LoggerFactory`를 사용하여 `ILogger`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="06cbd-132">다음 ASP.NET Core 예제는 `TodoApiSample.Pages.AboutModel`을 범주로 사용하여 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="06cbd-133">로그 *범주*는 각 로그와 연결된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="06cbd-134">DI에서 제공한 `ILogger<T>` 인스턴스는 `T` 형식의 정규화된 이름을 범주로 가진 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="06cbd-135">다음의 비 호스트 콘솔 앱 예제는 `LoggingConsoleApp.Program`을 범주로 사용하는 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="06cbd-136">다음의 ASP.NET Core 및 콘솔 앱 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="06cbd-137">로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="06cbd-138">[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="06cbd-139">Program 클래스에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-139">Create logs in the Program class</span></span>

<span data-ttu-id="06cbd-140">ASP.NET Core 앱의 `Program` 클래스에서 로그를 작성하려면 호스트를 빌드한 후 DI에서 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="06cbd-141">호스트 생성 중 로깅은 직접 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="06cbd-142">그러나 별도의 로거를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-142">However, a separate logger can be used.</span></span> <span data-ttu-id="06cbd-143">다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateHostBuilder`에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="06cbd-144">`AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="06cbd-145">Startup 클래스에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-145">Create logs in the Startup class</span></span>

<span data-ttu-id="06cbd-146">ASP.NET Core 앱의 `Startup.Configure` 메서드에서 로그를 작성하려면 메서드 시그니처에 `ILogger` 매개 변수를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="06cbd-147">`Startup.ConfigureServices` 메서드에서 DI 컨테이너 설정이 완료되기 전에 로그를 작성하는 작업은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="06cbd-148">`Startup` 생성자에 대한 로거 주입은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="06cbd-149">`Startup.ConfigureServices` 메서드 시그니처에 대한 로거 주입은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="06cbd-150">이러한 제한의 이유는 로깅이 DI와 구성에 종속되며, 이는 결국 DI에 종속되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="06cbd-151">DI 컨테이너는 `ConfigureServices`가 완료될 때까지 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="06cbd-152">이전 버전의 ASP.NET Core에서는 `Startup`에 대한 로거의 생성자 주입이 작동하는데, 이는 웹 호스트에 대한 별도의 DI 컨테이너가 생성되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="06cbd-153">제네릭 호스트에 대해 하나의 컨테이너만 생성되는 이유에 대한 자세한 내용은 [호환성이 손상되는 변경 공지](https://github.com/aspnet/Announcements/issues/353)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="06cbd-154">`ILogger<T>`에 종속된 서비스를 구성해야 하는 경우에도 여전히 생성자 주입을 사용하거나 팩터리 메서드를 제공하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="06cbd-155">팩터리 메서드 접근 방식은 다른 옵션이 없는 경우에만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="06cbd-156">예를 들어 DI의 서비스를 사용하여 속성을 채워야 한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="06cbd-157">위에 강조 표시된 코드 `Func`는 DI 컨테이너가 `MyService`의 인스턴스를 처음 생성해야 할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="06cbd-158">이러한 방식으로 등록된 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="06cbd-159">Blazor에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="06cbd-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="06cbd-160">Blazor WebAssembly</span></span>

<span data-ttu-id="06cbd-161">`Program.Main`의 `WebAssemblyHostBuilder.Logging` 속성을 사용하여 Blazor WebAssembly 앱에서 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="06cbd-162">`Logging` 속성은 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>형식이므로 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>에서 사용할 수 있는 모든 확장 메서드를 `Logging`에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="06cbd-163">Razor 구성 요소의 로그</span><span class="sxs-lookup"><span data-stu-id="06cbd-163">Log in Razor components</span></span>

<span data-ttu-id="06cbd-164">로거는 앱 시작 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="06cbd-165"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>와 같은 API의 Intellisense 작성을 지원하려면 <xref:Microsoft.Extensions.Logging>의 `using` 지시문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="06cbd-166">다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILogger>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="06cbd-167">다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="06cbd-168">비동기 로거 메서드 미지원</span><span class="sxs-lookup"><span data-stu-id="06cbd-168">No asynchronous logger methods</span></span>

<span data-ttu-id="06cbd-169">로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="06cbd-170">로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="06cbd-171">로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="06cbd-172">예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="06cbd-173">대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="06cbd-174">자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="06cbd-175">Configuration</span><span class="sxs-lookup"><span data-stu-id="06cbd-175">Configuration</span></span>

<span data-ttu-id="06cbd-176">로깅 공급자 구성은 하나 이상의 구성 공급자에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="06cbd-177">파일 형식(INI, JSON 및 XML).</span><span class="sxs-lookup"><span data-stu-id="06cbd-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="06cbd-178">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="06cbd-178">Command-line arguments.</span></span>
* <span data-ttu-id="06cbd-179">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="06cbd-179">Environment variables.</span></span>
* <span data-ttu-id="06cbd-180">메모리 내 .NET 개체.</span><span class="sxs-lookup"><span data-stu-id="06cbd-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="06cbd-181">암호화되지 않은 [암호 관리자](xref:security/app-secrets) 저장소.</span><span class="sxs-lookup"><span data-stu-id="06cbd-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="06cbd-182">암호화된 사용자 저장소(예:[Azure Key Vault](xref:security/key-vault-configuration)).</span><span class="sxs-lookup"><span data-stu-id="06cbd-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="06cbd-183">사용자 지정 공급자(설치 또는 생성된).</span><span class="sxs-lookup"><span data-stu-id="06cbd-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="06cbd-184">예를 들어 로깅 구성은 일반적으로 앱 설정 파일의 `Logging` 섹션에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="06cbd-185">다음 예제에서는 일반적인 *appsettings.Development.json* 파일의 콘텐츠를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="06cbd-186">`Logging` 속성은 `LogLevel` 및 로그 공급자 속성을 포함할 수 있습니다(Console이 나타나 있습니다).</span><span class="sxs-lookup"><span data-stu-id="06cbd-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="06cbd-187">`Logging` 아래의 `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="06cbd-188">이 예제에서는 `System` 및 `Microsoft` 범주는 `Information` 수준으로 로그되고 다른 모든 범주는 `Debug` 수준으로 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="06cbd-189">`Logging` 아래의 다른 속성은 로깅 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="06cbd-190">이 예제는 콘솔 공급자에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-190">The example is for the Console provider.</span></span> <span data-ttu-id="06cbd-191">공급자가 [로그 범위](#log-scopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="06cbd-192">공급자 속성(예: 예제에서 `Console`)은 `LogLevel` 속성을 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="06cbd-193">공급자 아래의 `LogLevel`은 해당 공급자에 대한 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="06cbd-194">수준이 `Logging.{providername}.LogLevel`에 지정된 경우 `Logging.LogLevel`에 설정된 모든 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="06cbd-195">로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="06cbd-196">그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="06cbd-197">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 설정 파일을 읽기 위해 `CreateDefaultBuilder`에 의해 추가되며 기본적으로 로깅 구성을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="06cbd-198">앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="06cbd-199">구성 공급자 구현에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="06cbd-200">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="06cbd-200">Sample logging output</span></span>

<span data-ttu-id="06cbd-201">이전 섹션에 나와 있는 샘플 코드를 사용하면 명령줄에서 앱을 실행할 때 콘솔에 로그가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="06cbd-202">다음은 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="06cbd-203">위의 로그는 샘플 앱의 `http://localhost:5000/api/todo/0`에 HTTP Get 요청을 하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="06cbd-204">다음은 Visual Studio에서 샘플 앱을 실행할 때 디버그 창에 나타나는 것과 동일한 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="06cbd-205">이전 섹션에 표시된 `ILogger` 호출을 통해 생성된 로그는 "TodoApiSample"로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="06cbd-206">"Microsoft" 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="06cbd-207">ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="06cbd-208">이 문서의 나머지 부분에서는 로깅에 대한 일부 세부 정보 및 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="06cbd-209">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="06cbd-209">NuGet packages</span></span>

<span data-ttu-id="06cbd-210">`ILogger` 및 `ILoggerFactory` 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있으며, 두 인터페이스의 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="06cbd-211">로그 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-211">Log category</span></span>

<span data-ttu-id="06cbd-212">`ILogger` 개체가 생성되면 개체에 대한 *범주*가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="06cbd-213">해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="06cbd-214">범주는 임의의 문자열일 수 있지만 "TodoApi.Controllers.TodoController"와 같은 클래스 이름을 사용하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="06cbd-215">`ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="06cbd-216">범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="06cbd-217">`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="06cbd-218">로그 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-218">Log level</span></span>

<span data-ttu-id="06cbd-219">모든 로그는 <xref:Microsoft.Extensions.Logging.LogLevel> 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="06cbd-220">로그 수준은 심각도 또는 중요도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="06cbd-221">예를 들어 메서드가 정상적으로 종료되면 `Information` 로그를 작성하고 메서드가 *404 찾을 수 없음* 상태 코드를 반환하면 `Warning` 로그를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="06cbd-222">다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="06cbd-223">위의 코드에서 첫 번째 매개 변수는 [로그 이벤트 ID](#log-event-id)입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="06cbd-224">두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="06cbd-225">메서드 매개 변수는 이 문서의 뒷부분에 있는 [메시지 템플릿 섹션](#log-message-template)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="06cbd-226">메서드 이름에 수준을 포함하는 로그 메서드(예: `LogInformation` 및 `LogWarning`)는 [ILogger에 대한 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="06cbd-227">이 메서드들은 `LogLevel` 매개 변수를 사용하는 `Log` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="06cbd-228">이러한 확장 메서드 중 하나를 호출하는 대신 `Log` 메서드를 직접 호출할 수도 있지만, 구문이 비교적 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="06cbd-229">자세한 내용은 <xref:Microsoft.Extensions.Logging.ILogger> 및 [로거 확장 소스 코드](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="06cbd-230">ASP.NET Core는 다음과 같은 로그 수준을 정의하며, 여기에는 가장 낮은 심각도에서 가장 높은 심각도 순으로 정렬되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="06cbd-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="06cbd-231">Trace = 0</span></span>

  <span data-ttu-id="06cbd-232">일반적으로 디버깅에만 유용한 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="06cbd-233">이러한 메시지는 중요한 애플리케이션 데이터를 포함할 수 있으므로 프로덕션 환경에서 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="06cbd-234">*기본적으로 사용하지 않도록 설정됩니다.*</span><span class="sxs-lookup"><span data-stu-id="06cbd-234">*Disabled by default.*</span></span>

* <span data-ttu-id="06cbd-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="06cbd-235">Debug = 1</span></span>

  <span data-ttu-id="06cbd-236">개발 및 디버깅에 유용할 수 있는 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="06cbd-237">예: `Entering method Configure with flag set to true.` 로그 볼륨이 크기 때문에 문제 해결 시에만 `Debug` 수준 로그를 프로덕션 환경에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="06cbd-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="06cbd-238">Information = 2</span></span>

  <span data-ttu-id="06cbd-239">앱의 일반적인 흐름을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="06cbd-240">이러한 로그는 일반적으로 장기적인 가치가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="06cbd-241">예: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="06cbd-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="06cbd-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="06cbd-242">Warning = 3</span></span>

  <span data-ttu-id="06cbd-243">앱의 흐름에서 비정상적이거나 예기치 않은 이벤트를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="06cbd-244">앱이 중지되지는 않지만 조사해야 하는 오류 또는 기타 조건이 여기에 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="06cbd-245">처리된 예외는 `Warning` 로그 수준을 사용하는 일반적인 장소입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="06cbd-246">예: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="06cbd-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="06cbd-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="06cbd-247">Error = 4</span></span>

  <span data-ttu-id="06cbd-248">처리할 수 없는 오류 및 예외를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="06cbd-249">이러한 메시지는 전체 앱 오류가 아닌 현재 동작 또는 작업(예: 현재 HTTP 요청)의 오류를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="06cbd-250">예제 로그 메시지: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="06cbd-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="06cbd-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="06cbd-251">Critical = 5</span></span>

  <span data-ttu-id="06cbd-252">즉각적인 대응이 필요한 오류를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-252">For failures that require immediate attention.</span></span> <span data-ttu-id="06cbd-253">예: 데이터 손실 시나리오, 디스크 공간 부족.</span><span class="sxs-lookup"><span data-stu-id="06cbd-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="06cbd-254">로그 수준을 사용하여 특정 스토리지 매체 또는 디스플레이 창에 기록되는 로그 출력의 양을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="06cbd-255">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="06cbd-255">For example:</span></span>

* <span data-ttu-id="06cbd-256">프로덕션:</span><span class="sxs-lookup"><span data-stu-id="06cbd-256">In production:</span></span>
  * <span data-ttu-id="06cbd-257">`Trace`~`Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="06cbd-258">비용을 관리하고 데이터 저장소 제한을 초과하지 않으려면 `Trace`~`Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="06cbd-259">`Warning`~`Critical` 수준 로깅에서 생성되는 로그 메시지는 일반적으로 수량 및 용량이 더 적습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="06cbd-260">따라서 비용 및 스토리지 제한은 대개의 경우 문제가 되지 않으므로 데이터 스토리지를 유연하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="06cbd-261">개발 중:</span><span class="sxs-lookup"><span data-stu-id="06cbd-261">During development:</span></span>
  * <span data-ttu-id="06cbd-262">`Warning`~`Critical` 메시지를 콘솔에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="06cbd-263">문제 해결 시 `Trace`~`Information` 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="06cbd-264">이 문서의 뒷부분에 나오는 [로그 필터링](#log-filtering) 섹션에서는 공급자가 처리하는 로그 수준을 제어하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="06cbd-265">ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="06cbd-266">이 문서 앞부분에 나온 로그 예제는 `Information` 수준 미만 로그를 제외했기 때문에 `Debug` 또는 `Trace` 수준 로그가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="06cbd-267">다음은 `Debug` 로그를 표시하도록 구성된 샘플 앱을 실행하여 생성된 콘솔 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="06cbd-268">로그 이벤트 ID</span><span class="sxs-lookup"><span data-stu-id="06cbd-268">Log event ID</span></span>

<span data-ttu-id="06cbd-269">각 로그는 *이벤트 ID*를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="06cbd-270">샘플 앱은 로컬에 정의된 `LoggingEvents` 클래스를 사용하여 이 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="06cbd-271">이벤트 ID는 이벤트 집합을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-271">An event ID associates a set of events.</span></span> <span data-ttu-id="06cbd-272">예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="06cbd-273">로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="06cbd-274">디버그 공급자는 이벤트 ID를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="06cbd-275">콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="06cbd-276">로그 메시지 템플릿</span><span class="sxs-lookup"><span data-stu-id="06cbd-276">Log message template</span></span>

<span data-ttu-id="06cbd-277">각 로그는 메시지 템플릿을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-277">Each log specifies a message template.</span></span> <span data-ttu-id="06cbd-278">메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="06cbd-279">번호가 아닌 자리 표시자의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="06cbd-280">값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="06cbd-281">다음 코드에서는 매개 변수 이름이 메시지 템플릿 순서와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="06cbd-282">이 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="06cbd-283">로깅 프레임워크는 로깅 공급자가 [구조화된 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있도록 이 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="06cbd-284">인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="06cbd-285">이 정보를 통해 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="06cbd-286">예를 들어 로거 메서드 호출이 다음과 같다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="06cbd-287">Azure Table Storage에 로그를 보내는 경우 각 Azure Table 엔터티는 로그 데이터에 대한 쿼리를 간소화하는 `ID` 및 `RequestTime` 속성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="06cbd-288">쿼리는 문자 메시지의 시간 초과를 구문 분석하지 않고 특정 `RequestTime` 범위 내의 모든 로그를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="06cbd-289">예외 로깅</span><span class="sxs-lookup"><span data-stu-id="06cbd-289">Logging exceptions</span></span>

<span data-ttu-id="06cbd-290">로거 메서드는 다음 예제와 같이 예외를 전달할 수 있는 오버로드를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="06cbd-291">공급자마다 다양한 방식으로 예외 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="06cbd-292">다음은 위에서 살펴본 코드의 디버그 공급자 출력의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="06cbd-293">로그 필터링</span><span class="sxs-lookup"><span data-stu-id="06cbd-293">Log filtering</span></span>

<span data-ttu-id="06cbd-294">특정 공급자 및 범주 또는 모든 공급자나 모든 범주의 최소 로그 수준을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="06cbd-295">최소 수준 미만인 로그는 해당 공급자에게 전달되지 않으므로 표시되거나 저장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="06cbd-296">모든 로그를 표시하지 않으려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="06cbd-297">`LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="06cbd-298">구성에서 필터 규칙 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-298">Create filter rules in configuration</span></span>

<span data-ttu-id="06cbd-299">프로젝트 템플릿 코드는 `CreateDefaultBuilder`를 호출하여 콘솔, 디버그 및 EventSource(ASP.NET Core 2.2 이상) 공급자에 대한 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="06cbd-300">[이 문서의 앞](#configuration)에서 설명한 것처럼 `CreateDefaultBuilder` 메서드는 `Logging` 섹션에서 구성을 조회하는 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="06cbd-301">구성 데이터는 다음 예제와 같이 공급자 및 범주별로 최소 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="06cbd-302">이 JSON은 6개의 필터 규칙을 만드는데, 하나는 디버그 공급자용이고, 4개는 콘솔 공급자용이고, 나머지 하나는 모든 공급자용입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="06cbd-303">`ILogger` 개체가 생성될 때 각 공급자에 대해 단일 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="06cbd-304">코드의 필터 규칙</span><span class="sxs-lookup"><span data-stu-id="06cbd-304">Filter rules in code</span></span>

<span data-ttu-id="06cbd-305">다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="06cbd-306">두 번째 `AddFilter`는 형식 이름을 사용하여 디버그 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="06cbd-307">첫 번째 `AddFilter`는 공급자 형식을 지정하지 않으며, 따라서 모든 공급자에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="06cbd-308">필터링 규칙 적용 방식</span><span class="sxs-lookup"><span data-stu-id="06cbd-308">How filtering rules are applied</span></span>

<span data-ttu-id="06cbd-309">이전 예제의 구성 데이터 및 `AddFilter` 코드는 다음 표에 표시된 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="06cbd-310">처음 6개는 구성 예제로부터 비롯된 것이고 마지막 2개는 코드 예제로부터 비롯된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="06cbd-311">번호</span><span class="sxs-lookup"><span data-stu-id="06cbd-311">Number</span></span> | <span data-ttu-id="06cbd-312">공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-312">Provider</span></span>      | <span data-ttu-id="06cbd-313">다음으로 시작하는 범주...</span><span class="sxs-lookup"><span data-stu-id="06cbd-313">Categories that begin with ...</span></span>          | <span data-ttu-id="06cbd-314">최소 로그 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="06cbd-315">1</span><span class="sxs-lookup"><span data-stu-id="06cbd-315">1</span></span>      | <span data-ttu-id="06cbd-316">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-316">Debug</span></span>         | <span data-ttu-id="06cbd-317">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-317">All categories</span></span>                          | <span data-ttu-id="06cbd-318">Information</span><span class="sxs-lookup"><span data-stu-id="06cbd-318">Information</span></span>       |
| <span data-ttu-id="06cbd-319">2</span><span class="sxs-lookup"><span data-stu-id="06cbd-319">2</span></span>      | <span data-ttu-id="06cbd-320">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-320">Console</span></span>       | <span data-ttu-id="06cbd-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="06cbd-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="06cbd-322">Warning</span><span class="sxs-lookup"><span data-stu-id="06cbd-322">Warning</span></span>           |
| <span data-ttu-id="06cbd-323">3</span><span class="sxs-lookup"><span data-stu-id="06cbd-323">3</span></span>      | <span data-ttu-id="06cbd-324">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-324">Console</span></span>       | <span data-ttu-id="06cbd-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="06cbd-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="06cbd-326">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-326">Debug</span></span>             |
| <span data-ttu-id="06cbd-327">4</span><span class="sxs-lookup"><span data-stu-id="06cbd-327">4</span></span>      | <span data-ttu-id="06cbd-328">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-328">Console</span></span>       | <span data-ttu-id="06cbd-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="06cbd-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="06cbd-330">Error</span><span class="sxs-lookup"><span data-stu-id="06cbd-330">Error</span></span>             |
| <span data-ttu-id="06cbd-331">5</span><span class="sxs-lookup"><span data-stu-id="06cbd-331">5</span></span>      | <span data-ttu-id="06cbd-332">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-332">Console</span></span>       | <span data-ttu-id="06cbd-333">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-333">All categories</span></span>                          | <span data-ttu-id="06cbd-334">정보</span><span class="sxs-lookup"><span data-stu-id="06cbd-334">Information</span></span>       |
| <span data-ttu-id="06cbd-335">6</span><span class="sxs-lookup"><span data-stu-id="06cbd-335">6</span></span>      | <span data-ttu-id="06cbd-336">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-336">All providers</span></span> | <span data-ttu-id="06cbd-337">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-337">All categories</span></span>                          | <span data-ttu-id="06cbd-338">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-338">Debug</span></span>             |
| <span data-ttu-id="06cbd-339">7</span><span class="sxs-lookup"><span data-stu-id="06cbd-339">7</span></span>      | <span data-ttu-id="06cbd-340">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-340">All providers</span></span> | <span data-ttu-id="06cbd-341">시스템</span><span class="sxs-lookup"><span data-stu-id="06cbd-341">System</span></span>                                  | <span data-ttu-id="06cbd-342">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-342">Debug</span></span>             |
| <span data-ttu-id="06cbd-343">8</span><span class="sxs-lookup"><span data-stu-id="06cbd-343">8</span></span>      | <span data-ttu-id="06cbd-344">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-344">Debug</span></span>         | <span data-ttu-id="06cbd-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="06cbd-345">Microsoft</span></span>                               | <span data-ttu-id="06cbd-346">Trace</span><span class="sxs-lookup"><span data-stu-id="06cbd-346">Trace</span></span>             |

<span data-ttu-id="06cbd-347">`ILogger` 개체를 만들 때 `ILoggerFactory` 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="06cbd-348">`ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="06cbd-349">사용 가능한 규칙 중에서 각 공급자 및 범주 쌍에 적용 가능한 가장 구체적인 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="06cbd-350">지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="06cbd-351">공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="06cbd-352">일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="06cbd-353">앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="06cbd-354">일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="06cbd-355">여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="06cbd-356">규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="06cbd-357">앞의 규칙 목록을 사용하여 "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대한 `ILogger` 개체를 만든다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="06cbd-358">디버그 공급자에게는 규칙 1, 6, 8이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="06cbd-359">규칙 8이 가장 구체적이므로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="06cbd-360">콘솔 공급자에게는 규칙 3, 4, 5, 6이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="06cbd-361">규칙 3이 가장 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="06cbd-362">결과 `ILogger` 인스턴스는 `Trace` 수준 이상의 로그를 디버그 공급자에게 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="06cbd-363">`Debug` 수준 이상의 로그는 콘솔 공급자에게 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="06cbd-364">공급자 별칭</span><span class="sxs-lookup"><span data-stu-id="06cbd-364">Provider aliases</span></span>

<span data-ttu-id="06cbd-365">각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="06cbd-366">기본 공급자의 경우 다음 별칭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="06cbd-367">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-367">Console</span></span>
* <span data-ttu-id="06cbd-368">Debug</span><span class="sxs-lookup"><span data-stu-id="06cbd-368">Debug</span></span>
* <span data-ttu-id="06cbd-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-369">EventSource</span></span>
* <span data-ttu-id="06cbd-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="06cbd-370">EventLog</span></span>
* <span data-ttu-id="06cbd-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-371">TraceSource</span></span>
* <span data-ttu-id="06cbd-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="06cbd-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="06cbd-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="06cbd-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="06cbd-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="06cbd-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="06cbd-375">기본 최소 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-375">Default minimum level</span></span>

<span data-ttu-id="06cbd-376">특정 공급자 및 범주에 대한 구성 또는 코드의 규칙이 적용되지 않는 경우에만 적용되는 최소 수준 설정이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="06cbd-377">다음 예제는 최소 수준을 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="06cbd-378">최소 수준을 명시적으로 설정하지 않으면 `Trace` 및 `Debug` 로그를 무시하는 `Information`이 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="06cbd-379">필터 함수</span><span class="sxs-lookup"><span data-stu-id="06cbd-379">Filter functions</span></span>

<span data-ttu-id="06cbd-380">필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="06cbd-381">함수의 코드는 공급자 형식, 범주 및 로그 수준에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="06cbd-382">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="06cbd-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="06cbd-383">시스템 범주 및 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-383">System categories and levels</span></span>

<span data-ttu-id="06cbd-384">다음은 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주로, 예상되는 로그에 대한 참고 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="06cbd-385">범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-385">Category</span></span>                            | <span data-ttu-id="06cbd-386">참고</span><span class="sxs-lookup"><span data-stu-id="06cbd-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="06cbd-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="06cbd-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="06cbd-388">일반 ASP.NET Core 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="06cbd-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="06cbd-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="06cbd-390">고려되고, 발견되고, 사용된 키.</span><span class="sxs-lookup"><span data-stu-id="06cbd-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="06cbd-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="06cbd-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="06cbd-392">허용되는 호스트.</span><span class="sxs-lookup"><span data-stu-id="06cbd-392">Hosts allowed.</span></span> |
| <span data-ttu-id="06cbd-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="06cbd-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="06cbd-394">HTTP 요청을 완료하는 데 걸린 시간과 시작 시간.</span><span class="sxs-lookup"><span data-stu-id="06cbd-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="06cbd-395">로드된 호스팅 시작 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="06cbd-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="06cbd-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="06cbd-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="06cbd-397">MVC 및 Razor 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="06cbd-398">모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택.</span><span class="sxs-lookup"><span data-stu-id="06cbd-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="06cbd-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="06cbd-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="06cbd-400">경로 일치 정보.</span><span class="sxs-lookup"><span data-stu-id="06cbd-400">Route matching information.</span></span> |
| <span data-ttu-id="06cbd-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="06cbd-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="06cbd-402">연결 시작, 중지 및 활성 응답 유지.</span><span class="sxs-lookup"><span data-stu-id="06cbd-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="06cbd-403">HTTPS 인증서 정보.</span><span class="sxs-lookup"><span data-stu-id="06cbd-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="06cbd-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="06cbd-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="06cbd-405">제공된 파일.</span><span class="sxs-lookup"><span data-stu-id="06cbd-405">Files served.</span></span> |
| <span data-ttu-id="06cbd-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="06cbd-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="06cbd-407">일반 Entity Framework Core 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="06cbd-408">데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션.</span><span class="sxs-lookup"><span data-stu-id="06cbd-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="06cbd-409">로그 범위</span><span class="sxs-lookup"><span data-stu-id="06cbd-409">Log scopes</span></span>

 <span data-ttu-id="06cbd-410">*범위*는 논리적 작업 집합을 그룹화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="06cbd-411">이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="06cbd-412">예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="06cbd-413">범위는 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 `IDisposable` 형식이며 삭제될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="06cbd-414">`using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="06cbd-415">다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="06cbd-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="06cbd-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="06cbd-417">범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="06cbd-418">구성에 대한 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="06cbd-419">각 로그 메시지는 범위 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="06cbd-420">기본 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-420">Built-in logging providers</span></span>

<span data-ttu-id="06cbd-421">ASP.NET Core는 다음 공급자를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="06cbd-422">콘솔</span><span class="sxs-lookup"><span data-stu-id="06cbd-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="06cbd-423">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="06cbd-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="06cbd-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="06cbd-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="06cbd-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="06cbd-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="06cbd-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="06cbd-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="06cbd-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="06cbd-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="06cbd-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="06cbd-430">ASP.NET Core 모듈을 사용한 표준 출력(stdout) 및 디버그 로깅에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="06cbd-431">콘솔 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-431">Console provider</span></span>

<span data-ttu-id="06cbd-432">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지는 콘솔에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="06cbd-433">콘솔 로깅 출력을 보려면 프로젝트 폴더에서 명령 프롬프트를 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="06cbd-434">디버그 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-434">Debug provider</span></span>

<span data-ttu-id="06cbd-435">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스(`Debug.WriteLine` 메서드 호출)를 사용하여 로그 출력을 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="06cbd-436">Linux에서 이 공급자는 */var/log/message*에 로그를 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="06cbd-437">이벤트 원본 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-437">Event Source provider</span></span>

<span data-ttu-id="06cbd-438">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지는 `Microsoft-Extensions-Logging` 이름을 사용하여 플랫폼 간 이벤트 원본에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="06cbd-439">Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="06cbd-440">호스트를 빌드하기 위해 `CreateDefaultBuilder`가 호출되면 이벤트 원본 공급자가 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="06cbd-441">dotnet 추적 도구</span><span class="sxs-lookup"><span data-stu-id="06cbd-441">dotnet trace tooling</span></span>

<span data-ttu-id="06cbd-442">[dotnet 추적](/dotnet/core/diagnostics/dotnet-trace) 도구는 실행 중인 프로세스의 .NET Core 추적을 수집할 수 있도록 하는 플랫폼 간 CLI 전역 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="06cbd-443">이 도구는 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>를 사용하여 <xref:Microsoft.Extensions.Logging.EventSource> 공급자 데이터를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="06cbd-444">다음 명령을 사용하여 dotnet 추적 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="06cbd-445">Dotnet 추적 도구를 사용하여 앱에서 추적을 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="06cbd-446">앱이 `CreateDefaultBuilder`를 사용하여 호스트를 빌드하지 않는 경우 [이벤트 원본 공급자](#event-source-provider)를 앱의 로깅 구성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="06cbd-447">`dotnet run` 명령으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="06cbd-448">.NET Core 앱의 PID(프로세스 식별자)를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="06cbd-449">Windows에서 다음 접근 방식 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="06cbd-450">작업 관리자(Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="06cbd-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="06cbd-451">tasklist 명령</span><span class="sxs-lookup"><span data-stu-id="06cbd-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="06cbd-452">Get-Process Powershell 명령</span><span class="sxs-lookup"><span data-stu-id="06cbd-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="06cbd-453">Linux에서는 [pidof 명령](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="06cbd-454">앱의 어셈블리와 동일한 이름의 프로세스에 대한 PID를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="06cbd-455">`dotnet trace` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="06cbd-456">일반 명령 구문:</span><span class="sxs-lookup"><span data-stu-id="06cbd-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="06cbd-457">PowerShell 명령 셸을 사용하는 경우 `--providers` 값을 작은따옴표(`'`)로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="06cbd-458">Windows가 아닌 플랫폼에서는 출력 추적 파일의 형식을 `speedscope`로 변경하는 `-f speedscope` 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="06cbd-459">키워드</span><span class="sxs-lookup"><span data-stu-id="06cbd-459">Keyword</span></span> | <span data-ttu-id="06cbd-460">설명</span><span class="sxs-lookup"><span data-stu-id="06cbd-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="06cbd-461">1</span><span class="sxs-lookup"><span data-stu-id="06cbd-461">1</span></span>       | <span data-ttu-id="06cbd-462">`LoggingEventSource`에 대한 로그 메타 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="06cbd-463">`ILogger`)에서 이벤트를 기록하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="06cbd-464">2</span><span class="sxs-lookup"><span data-stu-id="06cbd-464">2</span></span>       | <span data-ttu-id="06cbd-465">`ILogger.Log()`가 호출될 때 `Message` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="06cbd-466">서식 지정되지 않은 프로그래밍 방식으로 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="06cbd-467">4</span><span class="sxs-lookup"><span data-stu-id="06cbd-467">4</span></span>       | <span data-ttu-id="06cbd-468">`ILogger.Log()`가 호출될 때 `FormatMessage` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="06cbd-469">서식 지정된 문자열 버전의 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="06cbd-470">8</span><span class="sxs-lookup"><span data-stu-id="06cbd-470">8</span></span>       | <span data-ttu-id="06cbd-471">`ILogger.Log()`가 호출될 때 `MessageJson` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="06cbd-472">인수의 JSON 표현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="06cbd-473">이벤트 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-473">Event Level</span></span> | <span data-ttu-id="06cbd-474">설명</span><span class="sxs-lookup"><span data-stu-id="06cbd-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="06cbd-475">0</span><span class="sxs-lookup"><span data-stu-id="06cbd-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="06cbd-476">1</span><span class="sxs-lookup"><span data-stu-id="06cbd-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="06cbd-477">2</span><span class="sxs-lookup"><span data-stu-id="06cbd-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="06cbd-478">3</span><span class="sxs-lookup"><span data-stu-id="06cbd-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="06cbd-479">4</span><span class="sxs-lookup"><span data-stu-id="06cbd-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="06cbd-480">5</span><span class="sxs-lookup"><span data-stu-id="06cbd-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="06cbd-481">`FilterSpecs``{Logger Category}` 및 `{Event Level}`에 대한 항목은 추가 로그 필터링 조건을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="06cbd-482">`FilterSpecs` 항목을 세미콜론(`;`)으로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="06cbd-483">Windows 명령 셸을 사용하는 예(`--providers` 값에 작은따옴표를 사용하지 **않음**):</span><span class="sxs-lookup"><span data-stu-id="06cbd-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="06cbd-484">이전 명령은 다음과 같이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-484">The preceding command activates:</span></span>

   * <span data-ttu-id="06cbd-485">(`2`) 오류에 대해 형식이 지정된 문자열(`4`)을 생성하는 이벤트 원본 로거입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="06cbd-486">`Informational` 로깅 수준에서 `Microsoft.AspNetCore.Hosting` 로깅(`4`).</span><span class="sxs-lookup"><span data-stu-id="06cbd-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="06cbd-487">Enter 키 또는 Ctrl + C를 눌러 dotnet 추적 도구를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="06cbd-488">추적은 `dotnet trace` 명령이 실행되는 폴더에 *trace.nettrace* 이름으로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="06cbd-489">[Perfview](#perfview)를 사용하여 추적을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="06cbd-490">*trace.nettrace* 파일을 열고 추적 이벤트를 탐색합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="06cbd-491">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-491">For more information, see:</span></span>

* <span data-ttu-id="06cbd-492">[성능 분석 유틸리티 추적(dotnet 추적)](/dotnet/core/diagnostics/dotnet-trace)(.NET Core 설명서)</span><span class="sxs-lookup"><span data-stu-id="06cbd-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="06cbd-493">[성능 분석 유틸리티 추적(dotnet 추적)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)(dotnet/진단 GitHub 리포지토리 설명서)</span><span class="sxs-lookup"><span data-stu-id="06cbd-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="06cbd-494">[LoggingEventSource 클래스](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)(.NET API 브라우저)</span><span class="sxs-lookup"><span data-stu-id="06cbd-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="06cbd-495">[LoggingEventSource 참조 소스(3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) - 다른 버전의 참조 소스를 가져오려면 분기를 `release/{Version}`으로 변경합니다. 여기서 `{Version}`은 원하는 ASP.NET Core 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="06cbd-496">[Perfview](#perfview) - 이벤트 원본 추적을 보는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="06cbd-497">Perfview</span><span class="sxs-lookup"><span data-stu-id="06cbd-497">Perfview</span></span>

<span data-ttu-id="06cbd-498">[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="06cbd-499">ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="06cbd-500">이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="06cbd-501">(문자열의 시작 부분에 별표를 누락하지 마세요.)</span><span class="sxs-lookup"><span data-stu-id="06cbd-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview 추가 공급자](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="06cbd-503">Windows EventLog 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-503">Windows EventLog provider</span></span>

<span data-ttu-id="06cbd-504">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="06cbd-505">[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)를 사용하여 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="06cbd-506">`null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="06cbd-507">`LogName` - “애플리케이션”</span><span class="sxs-lookup"><span data-stu-id="06cbd-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="06cbd-508">`SourceName` - “.NET 런타임”</span><span class="sxs-lookup"><span data-stu-id="06cbd-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="06cbd-509">`MachineName` - 로컬 머신</span><span class="sxs-lookup"><span data-stu-id="06cbd-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="06cbd-510">[경고 수준 이상](#log-level)의 이벤트가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="06cbd-511">`Warning` 수준보다 낮은 이벤트를 기록하려면 로그 수준을 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="06cbd-512">예를 들어 *appsettings.json* 파일에 다음 내용을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="06cbd-513">TraceSource 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-513">TraceSource provider</span></span>

<span data-ttu-id="06cbd-514">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지는 <xref:System.Diagnostics.TraceSource> 라이브러리 및 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="06cbd-515">[AddTraceSource 오버로드](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)를 사용하여 원본 스위치 및 추적 수신기를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="06cbd-516">이 공급자를 사용하려면 앱이 .NET Framework(.NET Core가 아닌)에서 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="06cbd-517">공급자는 샘플 앱에 사용된 <xref:System.Diagnostics.TextWriterTraceListener>와 같은 다양한 [수신기](/dotnet/framework/debug-trace-profile/trace-listeners)로 메시지를 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="06cbd-518">Azure App Service 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-518">Azure App Service provider</span></span>

<span data-ttu-id="06cbd-519">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="06cbd-520">이 공급자 패키지는 공유 프레임워크에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="06cbd-521">이 공급자를 사용하려면 프로젝트에 공급자 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="06cbd-522">공급자 설정을 구성하려면 다음 예제와 같이 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 및 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="06cbd-523">App Service 앱에 배포할 때 애플리케이션은 Azure Portal **App Service** 페이지의 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) 섹션에 있는 설정을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="06cbd-524">다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="06cbd-525">**애플리케이션 로깅(파일 시스템)**</span><span class="sxs-lookup"><span data-stu-id="06cbd-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="06cbd-526">**애플리케이션 로깅(Blob)**</span><span class="sxs-lookup"><span data-stu-id="06cbd-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="06cbd-527">로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="06cbd-528">기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="06cbd-529">기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="06cbd-530">공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="06cbd-531">프로젝트를 로컬로 실행하는 경우에는 아무 영향도 없습니다&mdash;로컬 파일 또는 로컬 개발 스토리지에 Blob을 기록하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="06cbd-532">Azure 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="06cbd-532">Azure log streaming</span></span>

<span data-ttu-id="06cbd-533">Azure 로그 스트리밍을 통해 로그 작업을 실시간으로 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="06cbd-534">앱 서버</span><span class="sxs-lookup"><span data-stu-id="06cbd-534">The app server</span></span>
* <span data-ttu-id="06cbd-535">웹 서버</span><span class="sxs-lookup"><span data-stu-id="06cbd-535">The web server</span></span>
* <span data-ttu-id="06cbd-536">실패한 요청 추적</span><span class="sxs-lookup"><span data-stu-id="06cbd-536">Failed request tracing</span></span>

<span data-ttu-id="06cbd-537">Azure 로그 스트리밍을 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="06cbd-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="06cbd-538">앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="06cbd-539">**애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="06cbd-540">로그 **수준**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-540">Choose the log **Level**.</span></span> <span data-ttu-id="06cbd-541">이 설정은 앱의 다른 로깅 공급자가 아닌 Azure 로그 스트리밍에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="06cbd-542">**로그 스트림** 페이지로 이동하여 앱 메시지를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="06cbd-543">앱이 `ILogger` 인터페이스를 통해 기록한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="06cbd-544">Azure Application Insights 추적 로깅</span><span class="sxs-lookup"><span data-stu-id="06cbd-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="06cbd-545">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 Azure Application Insights에 로그를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="06cbd-546">Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="06cbd-547">이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="06cbd-548">로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="06cbd-549">이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="06cbd-550">ASP.NET 4.x용으로 제공되는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지&mdash;를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="06cbd-551">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="06cbd-552">Application Insights 개요</span><span class="sxs-lookup"><span data-stu-id="06cbd-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="06cbd-553">[ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="06cbd-554">[.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="06cbd-555">[Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="06cbd-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="06cbd-556">[Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="06cbd-557">타사 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-557">Third-party logging providers</span></span>

<span data-ttu-id="06cbd-558">ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="06cbd-559">[elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="06cbd-561">[JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="06cbd-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="06cbd-562">[KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="06cbd-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="06cbd-563">[Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="06cbd-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="06cbd-564">[Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-565">[NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="06cbd-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="06cbd-567">[Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="06cbd-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="06cbd-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="06cbd-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="06cbd-569">일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="06cbd-570">타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="06cbd-571">프로젝트에 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="06cbd-572">로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="06cbd-573">자세한 내용은 각 공급자의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="06cbd-574">타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06cbd-575">추가 자료</span><span class="sxs-lookup"><span data-stu-id="06cbd-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="06cbd-576">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="06cbd-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="06cbd-577">.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="06cbd-578">이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="06cbd-579">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06cbd-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="06cbd-580">공급자 추가</span><span class="sxs-lookup"><span data-stu-id="06cbd-580">Add providers</span></span>

<span data-ttu-id="06cbd-581">로깅 공급자는 로그를 표시하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="06cbd-582">예를 들어 콘솔 공급자는 콘솔에 로그를 표시하고 Azure Application Insights 공급자는 로그를 Azure Application Insights에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="06cbd-583">여러 공급자를 추가하여 로그를 여러 대상으로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="06cbd-584">공급자를 추가하려면 *Program.cs*에서 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="06cbd-585">앞의 코드는 `Microsoft.Extensions.Logging` 및 `Microsoft.Extensions.Configuration`에 대한 참조를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="06cbd-586">기본 프로젝트 템플릿은 다음과 같은 로깅 공급자를 추가하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="06cbd-587">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-587">Console</span></span>
* <span data-ttu-id="06cbd-588">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-588">Debug</span></span>
* <span data-ttu-id="06cbd-589">EventSource(ASP.NET Core 2.2부터 시작)</span><span class="sxs-lookup"><span data-stu-id="06cbd-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="06cbd-590">`CreateDefaultBuilder`를 사용하는 경우 기본 제공자를 사용자가 선택한 대로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="06cbd-591"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하고 원하는 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="06cbd-592">문서의 뒷부분에 나오는 [기본 제공 로깅 공급자](#built-in-logging-providers) 및 [타사 로깅 공급자](#third-party-logging-providers)에서 더 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="06cbd-593">로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-593">Create logs</span></span>

<span data-ttu-id="06cbd-594">로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="06cbd-595">웹앱 또는 호스트되는 서비스에서는 DI(종속성 주입)로부터 `ILogger`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="06cbd-596">비 호스트 콘솔 앱에서는 `LoggerFactory`를 사용하여 `ILogger`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="06cbd-597">다음 ASP.NET Core 예제는 `TodoApiSample.Pages.AboutModel`을 범주로 사용하여 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="06cbd-598">로그 *범주*는 각 로그와 연결된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="06cbd-599">DI에서 제공한 `ILogger<T>` 인스턴스는 `T` 형식의 정규화된 이름을 범주로 가진 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="06cbd-600">다음의 ASP.NET Core 및 콘솔 앱 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="06cbd-601">로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="06cbd-602">[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="06cbd-603">시작 시 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-603">Create logs in Startup</span></span>

<span data-ttu-id="06cbd-604">`Startup` 클래스에서 로그를 작성하려면 생성자 시그니처에 `ILogger` 매개 변수를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="06cbd-605">Program 클래스에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-605">Create logs in the Program class</span></span>

<span data-ttu-id="06cbd-606">`Program` 클래스에 로그를 작성하려면 DI에서 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="06cbd-607">호스트 생성 중 로깅은 직접 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="06cbd-608">그러나 별도의 로거를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-608">However, a separate logger can be used.</span></span> <span data-ttu-id="06cbd-609">다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateWebHostBuilder`에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="06cbd-610">`AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="06cbd-611">비동기 로거 메서드 미지원</span><span class="sxs-lookup"><span data-stu-id="06cbd-611">No asynchronous logger methods</span></span>

<span data-ttu-id="06cbd-612">로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="06cbd-613">로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="06cbd-614">로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="06cbd-615">예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="06cbd-616">대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="06cbd-617">자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="06cbd-618">Configuration</span><span class="sxs-lookup"><span data-stu-id="06cbd-618">Configuration</span></span>

<span data-ttu-id="06cbd-619">로깅 공급자 구성은 하나 이상의 구성 공급자에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="06cbd-620">파일 형식(INI, JSON 및 XML).</span><span class="sxs-lookup"><span data-stu-id="06cbd-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="06cbd-621">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="06cbd-621">Command-line arguments.</span></span>
* <span data-ttu-id="06cbd-622">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="06cbd-622">Environment variables.</span></span>
* <span data-ttu-id="06cbd-623">메모리 내 .NET 개체.</span><span class="sxs-lookup"><span data-stu-id="06cbd-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="06cbd-624">암호화되지 않은 [암호 관리자](xref:security/app-secrets) 저장소.</span><span class="sxs-lookup"><span data-stu-id="06cbd-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="06cbd-625">암호화된 사용자 저장소(예:[Azure Key Vault](xref:security/key-vault-configuration)).</span><span class="sxs-lookup"><span data-stu-id="06cbd-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="06cbd-626">사용자 지정 공급자(설치 또는 생성된).</span><span class="sxs-lookup"><span data-stu-id="06cbd-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="06cbd-627">예를 들어 로깅 구성은 일반적으로 앱 설정 파일의 `Logging` 섹션에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="06cbd-628">다음 예제에서는 일반적인 *appsettings.Development.json* 파일의 콘텐츠를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="06cbd-629">`Logging` 속성은 `LogLevel` 및 로그 공급자 속성을 포함할 수 있습니다(Console이 나타나 있습니다).</span><span class="sxs-lookup"><span data-stu-id="06cbd-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="06cbd-630">`Logging` 아래의 `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="06cbd-631">이 예제에서는 `System` 및 `Microsoft` 범주는 `Information` 수준으로 로그되고 다른 모든 범주는 `Debug` 수준으로 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="06cbd-632">`Logging` 아래의 다른 속성은 로깅 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="06cbd-633">이 예제는 콘솔 공급자에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-633">The example is for the Console provider.</span></span> <span data-ttu-id="06cbd-634">공급자가 [로그 범위](#log-scopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="06cbd-635">공급자 속성(예: 예제에서 `Console`)은 `LogLevel` 속성을 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="06cbd-636">공급자 아래의 `LogLevel`은 해당 공급자에 대한 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="06cbd-637">수준이 `Logging.{providername}.LogLevel`에 지정된 경우 `Logging.LogLevel`에 설정된 모든 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="06cbd-638">로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="06cbd-639">그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="06cbd-640">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 설정 파일을 읽기 위해 `CreateDefaultBuilder`에 의해 추가되며 기본적으로 로깅 구성을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="06cbd-641">앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="06cbd-642">구성 공급자 구현에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="06cbd-643">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="06cbd-643">Sample logging output</span></span>

<span data-ttu-id="06cbd-644">이전 섹션에 나와 있는 샘플 코드를 사용하면 명령줄에서 앱을 실행할 때 콘솔에 로그가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="06cbd-645">다음은 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-645">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="06cbd-646">위의 로그는 샘플 앱의 `http://localhost:5000/api/todo/0`에 HTTP Get 요청을 하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="06cbd-647">다음은 Visual Studio에서 샘플 앱을 실행할 때 디버그 창에 나타나는 것과 동일한 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="06cbd-648">이전 섹션에 표시된 `ILogger` 호출을 통해 생성된 로그는 "TodoApi"로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="06cbd-649">"Microsoft" 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="06cbd-650">ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="06cbd-651">이 문서의 나머지 부분에서는 로깅에 대한 일부 세부 정보 및 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="06cbd-652">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="06cbd-652">NuGet packages</span></span>

<span data-ttu-id="06cbd-653">`ILogger` 및 `ILoggerFactory` 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있으며, 두 인터페이스의 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="06cbd-654">로그 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-654">Log category</span></span>

<span data-ttu-id="06cbd-655">`ILogger` 개체가 생성되면 개체에 대한 *범주*가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="06cbd-656">해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="06cbd-657">범주는 임의의 문자열일 수 있지만 "TodoApi.Controllers.TodoController"와 같은 클래스 이름을 사용하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="06cbd-658">`ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="06cbd-659">범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="06cbd-660">`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="06cbd-661">로그 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-661">Log level</span></span>

<span data-ttu-id="06cbd-662">모든 로그는 <xref:Microsoft.Extensions.Logging.LogLevel> 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="06cbd-663">로그 수준은 심각도 또는 중요도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="06cbd-664">예를 들어 메서드가 정상적으로 종료되면 `Information` 로그를 작성하고 메서드가 *404 찾을 수 없음* 상태 코드를 반환하면 `Warning` 로그를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="06cbd-665">다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="06cbd-666">위의 코드에서 첫 번째 매개 변수는 [로그 이벤트 ID](#log-event-id)입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="06cbd-667">두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="06cbd-668">메서드 매개 변수는 이 문서의 뒷부분에 있는 [메시지 템플릿 섹션](#log-message-template)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="06cbd-669">메서드 이름에 수준을 포함하는 로그 메서드(예: `LogInformation` 및 `LogWarning`)는 [ILogger에 대한 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="06cbd-670">이 메서드들은 `LogLevel` 매개 변수를 사용하는 `Log` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="06cbd-671">이러한 확장 메서드 중 하나를 호출하는 대신 `Log` 메서드를 직접 호출할 수도 있지만, 구문이 비교적 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="06cbd-672">자세한 내용은 <xref:Microsoft.Extensions.Logging.ILogger> 및 [로거 확장 소스 코드](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="06cbd-673">ASP.NET Core는 다음과 같은 로그 수준을 정의하며, 여기에는 가장 낮은 심각도에서 가장 높은 심각도 순으로 정렬되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="06cbd-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="06cbd-674">Trace = 0</span></span>

  <span data-ttu-id="06cbd-675">일반적으로 디버깅에만 유용한 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="06cbd-676">이러한 메시지는 중요한 애플리케이션 데이터를 포함할 수 있으므로 프로덕션 환경에서 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="06cbd-677">*기본적으로 사용하지 않도록 설정됩니다.*</span><span class="sxs-lookup"><span data-stu-id="06cbd-677">*Disabled by default.*</span></span>

* <span data-ttu-id="06cbd-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="06cbd-678">Debug = 1</span></span>

  <span data-ttu-id="06cbd-679">개발 및 디버깅에 유용할 수 있는 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="06cbd-680">예: `Entering method Configure with flag set to true.` 로그 볼륨이 크기 때문에 문제 해결 시에만 `Debug` 수준 로그를 프로덕션 환경에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="06cbd-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="06cbd-681">Information = 2</span></span>

  <span data-ttu-id="06cbd-682">앱의 일반적인 흐름을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="06cbd-683">이러한 로그는 일반적으로 장기적인 가치가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="06cbd-684">예: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="06cbd-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="06cbd-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="06cbd-685">Warning = 3</span></span>

  <span data-ttu-id="06cbd-686">앱의 흐름에서 비정상적이거나 예기치 않은 이벤트를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="06cbd-687">앱이 중지되지는 않지만 조사해야 하는 오류 또는 기타 조건이 여기에 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="06cbd-688">처리된 예외는 `Warning` 로그 수준을 사용하는 일반적인 장소입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="06cbd-689">예: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="06cbd-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="06cbd-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="06cbd-690">Error = 4</span></span>

  <span data-ttu-id="06cbd-691">처리할 수 없는 오류 및 예외를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="06cbd-692">이러한 메시지는 전체 앱 오류가 아닌 현재 동작 또는 작업(예: 현재 HTTP 요청)의 오류를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="06cbd-693">예제 로그 메시지: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="06cbd-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="06cbd-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="06cbd-694">Critical = 5</span></span>

  <span data-ttu-id="06cbd-695">즉각적인 대응이 필요한 오류를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-695">For failures that require immediate attention.</span></span> <span data-ttu-id="06cbd-696">예: 데이터 손실 시나리오, 디스크 공간 부족.</span><span class="sxs-lookup"><span data-stu-id="06cbd-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="06cbd-697">로그 수준을 사용하여 특정 스토리지 매체 또는 디스플레이 창에 기록되는 로그 출력의 양을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="06cbd-698">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="06cbd-698">For example:</span></span>

* <span data-ttu-id="06cbd-699">프로덕션:</span><span class="sxs-lookup"><span data-stu-id="06cbd-699">In production:</span></span>
  * <span data-ttu-id="06cbd-700">`Trace`~`Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="06cbd-701">비용을 관리하고 데이터 저장소 제한을 초과하지 않으려면 `Trace`~`Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="06cbd-702">`Warning`~`Critical` 수준 로깅에서 생성되는 로그 메시지는 일반적으로 수량 및 용량이 더 적습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="06cbd-703">따라서 비용 및 스토리지 제한은 대개의 경우 문제가 되지 않으므로 데이터 스토리지를 유연하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="06cbd-704">개발 중:</span><span class="sxs-lookup"><span data-stu-id="06cbd-704">During development:</span></span>
  * <span data-ttu-id="06cbd-705">`Warning`~`Critical` 메시지를 콘솔에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="06cbd-706">문제 해결 시 `Trace`~`Information` 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="06cbd-707">이 문서의 뒷부분에 나오는 [로그 필터링](#log-filtering) 섹션에서는 공급자가 처리하는 로그 수준을 제어하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="06cbd-708">ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="06cbd-709">이 문서 앞부분에 나온 로그 예제는 `Information` 수준 미만 로그를 제외했기 때문에 `Debug` 또는 `Trace` 수준 로그가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="06cbd-710">다음은 `Debug` 로그를 표시하도록 구성된 샘플 앱을 실행하여 생성된 콘솔 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="06cbd-711">로그 이벤트 ID</span><span class="sxs-lookup"><span data-stu-id="06cbd-711">Log event ID</span></span>

<span data-ttu-id="06cbd-712">각 로그는 *이벤트 ID*를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="06cbd-713">샘플 앱은 로컬에 정의된 `LoggingEvents` 클래스를 사용하여 이 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="06cbd-714">이벤트 ID는 이벤트 집합을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-714">An event ID associates a set of events.</span></span> <span data-ttu-id="06cbd-715">예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="06cbd-716">로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="06cbd-717">디버그 공급자는 이벤트 ID를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="06cbd-718">콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="06cbd-719">로그 메시지 템플릿</span><span class="sxs-lookup"><span data-stu-id="06cbd-719">Log message template</span></span>

<span data-ttu-id="06cbd-720">각 로그는 메시지 템플릿을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-720">Each log specifies a message template.</span></span> <span data-ttu-id="06cbd-721">메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="06cbd-722">번호가 아닌 자리 표시자의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="06cbd-723">값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="06cbd-724">다음 코드에서는 매개 변수 이름이 메시지 템플릿 순서와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="06cbd-725">이 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="06cbd-726">로깅 프레임워크는 로깅 공급자가 [구조화된 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있도록 이 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="06cbd-727">인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="06cbd-728">이 정보를 통해 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="06cbd-729">예를 들어 로거 메서드 호출이 다음과 같다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="06cbd-730">Azure Table Storage에 로그를 보내는 경우 각 Azure Table 엔터티는 로그 데이터에 대한 쿼리를 간소화하는 `ID` 및 `RequestTime` 속성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="06cbd-731">쿼리는 문자 메시지의 시간 초과를 구문 분석하지 않고 특정 `RequestTime` 범위 내의 모든 로그를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="06cbd-732">예외 로깅</span><span class="sxs-lookup"><span data-stu-id="06cbd-732">Logging exceptions</span></span>

<span data-ttu-id="06cbd-733">로거 메서드는 다음 예제와 같이 예외를 전달할 수 있는 오버로드를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="06cbd-734">공급자마다 다양한 방식으로 예외 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="06cbd-735">다음은 위에서 살펴본 코드의 디버그 공급자 출력의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="06cbd-736">로그 필터링</span><span class="sxs-lookup"><span data-stu-id="06cbd-736">Log filtering</span></span>

<span data-ttu-id="06cbd-737">특정 공급자 및 범주 또는 모든 공급자나 모든 범주의 최소 로그 수준을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="06cbd-738">최소 수준 미만인 로그는 해당 공급자에게 전달되지 않으므로 표시되거나 저장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="06cbd-739">모든 로그를 표시하지 않으려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="06cbd-740">`LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="06cbd-741">구성에서 필터 규칙 만들기</span><span class="sxs-lookup"><span data-stu-id="06cbd-741">Create filter rules in configuration</span></span>

<span data-ttu-id="06cbd-742">프로젝트 템플릿 코드는 `CreateDefaultBuilder`를 호출하여 콘솔, 디버그 및 EventSource(ASP.NET Core 2.2 이상) 공급자에 대한 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="06cbd-743">[이 문서의 앞](#configuration)에서 설명한 것처럼 `CreateDefaultBuilder` 메서드는 `Logging` 섹션에서 구성을 조회하는 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="06cbd-744">구성 데이터는 다음 예제와 같이 공급자 및 범주별로 최소 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="06cbd-745">이 JSON은 6개의 필터 규칙을 만드는데, 하나는 디버그 공급자용이고, 4개는 콘솔 공급자용이고, 나머지 하나는 모든 공급자용입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="06cbd-746">`ILogger` 개체가 생성될 때 각 공급자에 대해 단일 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="06cbd-747">코드의 필터 규칙</span><span class="sxs-lookup"><span data-stu-id="06cbd-747">Filter rules in code</span></span>

<span data-ttu-id="06cbd-748">다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="06cbd-749">두 번째 `AddFilter`는 형식 이름을 사용하여 디버그 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="06cbd-750">첫 번째 `AddFilter`는 공급자 형식을 지정하지 않으며, 따라서 모든 공급자에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="06cbd-751">필터링 규칙 적용 방식</span><span class="sxs-lookup"><span data-stu-id="06cbd-751">How filtering rules are applied</span></span>

<span data-ttu-id="06cbd-752">이전 예제의 구성 데이터 및 `AddFilter` 코드는 다음 표에 표시된 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="06cbd-753">처음 6개는 구성 예제로부터 비롯된 것이고 마지막 2개는 코드 예제로부터 비롯된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="06cbd-754">번호</span><span class="sxs-lookup"><span data-stu-id="06cbd-754">Number</span></span> | <span data-ttu-id="06cbd-755">공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-755">Provider</span></span>      | <span data-ttu-id="06cbd-756">다음으로 시작하는 범주...</span><span class="sxs-lookup"><span data-stu-id="06cbd-756">Categories that begin with ...</span></span>          | <span data-ttu-id="06cbd-757">최소 로그 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="06cbd-758">1</span><span class="sxs-lookup"><span data-stu-id="06cbd-758">1</span></span>      | <span data-ttu-id="06cbd-759">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-759">Debug</span></span>         | <span data-ttu-id="06cbd-760">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-760">All categories</span></span>                          | <span data-ttu-id="06cbd-761">Information</span><span class="sxs-lookup"><span data-stu-id="06cbd-761">Information</span></span>       |
| <span data-ttu-id="06cbd-762">2</span><span class="sxs-lookup"><span data-stu-id="06cbd-762">2</span></span>      | <span data-ttu-id="06cbd-763">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-763">Console</span></span>       | <span data-ttu-id="06cbd-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="06cbd-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="06cbd-765">Warning</span><span class="sxs-lookup"><span data-stu-id="06cbd-765">Warning</span></span>           |
| <span data-ttu-id="06cbd-766">3</span><span class="sxs-lookup"><span data-stu-id="06cbd-766">3</span></span>      | <span data-ttu-id="06cbd-767">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-767">Console</span></span>       | <span data-ttu-id="06cbd-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="06cbd-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="06cbd-769">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-769">Debug</span></span>             |
| <span data-ttu-id="06cbd-770">4</span><span class="sxs-lookup"><span data-stu-id="06cbd-770">4</span></span>      | <span data-ttu-id="06cbd-771">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-771">Console</span></span>       | <span data-ttu-id="06cbd-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="06cbd-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="06cbd-773">Error</span><span class="sxs-lookup"><span data-stu-id="06cbd-773">Error</span></span>             |
| <span data-ttu-id="06cbd-774">5</span><span class="sxs-lookup"><span data-stu-id="06cbd-774">5</span></span>      | <span data-ttu-id="06cbd-775">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-775">Console</span></span>       | <span data-ttu-id="06cbd-776">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-776">All categories</span></span>                          | <span data-ttu-id="06cbd-777">정보</span><span class="sxs-lookup"><span data-stu-id="06cbd-777">Information</span></span>       |
| <span data-ttu-id="06cbd-778">6</span><span class="sxs-lookup"><span data-stu-id="06cbd-778">6</span></span>      | <span data-ttu-id="06cbd-779">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-779">All providers</span></span> | <span data-ttu-id="06cbd-780">모든 범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-780">All categories</span></span>                          | <span data-ttu-id="06cbd-781">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-781">Debug</span></span>             |
| <span data-ttu-id="06cbd-782">7</span><span class="sxs-lookup"><span data-stu-id="06cbd-782">7</span></span>      | <span data-ttu-id="06cbd-783">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-783">All providers</span></span> | <span data-ttu-id="06cbd-784">시스템</span><span class="sxs-lookup"><span data-stu-id="06cbd-784">System</span></span>                                  | <span data-ttu-id="06cbd-785">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-785">Debug</span></span>             |
| <span data-ttu-id="06cbd-786">8</span><span class="sxs-lookup"><span data-stu-id="06cbd-786">8</span></span>      | <span data-ttu-id="06cbd-787">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-787">Debug</span></span>         | <span data-ttu-id="06cbd-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="06cbd-788">Microsoft</span></span>                               | <span data-ttu-id="06cbd-789">Trace</span><span class="sxs-lookup"><span data-stu-id="06cbd-789">Trace</span></span>             |

<span data-ttu-id="06cbd-790">`ILogger` 개체를 만들 때 `ILoggerFactory` 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="06cbd-791">`ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="06cbd-792">사용 가능한 규칙 중에서 각 공급자 및 범주 쌍에 적용 가능한 가장 구체적인 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="06cbd-793">지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="06cbd-794">공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="06cbd-795">일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="06cbd-796">앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="06cbd-797">일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="06cbd-798">여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="06cbd-799">규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="06cbd-800">앞의 규칙 목록을 사용하여 "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" 범주에 대한 `ILogger` 개체를 만든다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="06cbd-801">디버그 공급자에게는 규칙 1, 6, 8이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="06cbd-802">규칙 8이 가장 구체적이므로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="06cbd-803">콘솔 공급자에게는 규칙 3, 4, 5, 6이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="06cbd-804">규칙 3이 가장 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="06cbd-805">결과 `ILogger` 인스턴스는 `Trace` 수준 이상의 로그를 디버그 공급자에게 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="06cbd-806">`Debug` 수준 이상의 로그는 콘솔 공급자에게 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="06cbd-807">공급자 별칭</span><span class="sxs-lookup"><span data-stu-id="06cbd-807">Provider aliases</span></span>

<span data-ttu-id="06cbd-808">각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="06cbd-809">기본 공급자의 경우 다음 별칭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="06cbd-810">Console</span><span class="sxs-lookup"><span data-stu-id="06cbd-810">Console</span></span>
* <span data-ttu-id="06cbd-811">Debug</span><span class="sxs-lookup"><span data-stu-id="06cbd-811">Debug</span></span>
* <span data-ttu-id="06cbd-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-812">EventSource</span></span>
* <span data-ttu-id="06cbd-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="06cbd-813">EventLog</span></span>
* <span data-ttu-id="06cbd-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-814">TraceSource</span></span>
* <span data-ttu-id="06cbd-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="06cbd-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="06cbd-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="06cbd-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="06cbd-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="06cbd-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="06cbd-818">기본 최소 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-818">Default minimum level</span></span>

<span data-ttu-id="06cbd-819">특정 공급자 및 범주에 대한 구성 또는 코드의 규칙이 적용되지 않는 경우에만 적용되는 최소 수준 설정이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="06cbd-820">다음 예제는 최소 수준을 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="06cbd-821">최소 수준을 명시적으로 설정하지 않으면 `Trace` 및 `Debug` 로그를 무시하는 `Information`이 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="06cbd-822">필터 함수</span><span class="sxs-lookup"><span data-stu-id="06cbd-822">Filter functions</span></span>

<span data-ttu-id="06cbd-823">필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="06cbd-824">함수의 코드는 공급자 형식, 범주 및 로그 수준에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="06cbd-825">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="06cbd-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="06cbd-826">시스템 범주 및 수준</span><span class="sxs-lookup"><span data-stu-id="06cbd-826">System categories and levels</span></span>

<span data-ttu-id="06cbd-827">다음은 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주로, 예상되는 로그에 대한 참고 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="06cbd-828">범주</span><span class="sxs-lookup"><span data-stu-id="06cbd-828">Category</span></span>                            | <span data-ttu-id="06cbd-829">참고</span><span class="sxs-lookup"><span data-stu-id="06cbd-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="06cbd-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="06cbd-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="06cbd-831">일반 ASP.NET Core 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="06cbd-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="06cbd-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="06cbd-833">고려되고, 발견되고, 사용된 키.</span><span class="sxs-lookup"><span data-stu-id="06cbd-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="06cbd-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="06cbd-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="06cbd-835">허용되는 호스트.</span><span class="sxs-lookup"><span data-stu-id="06cbd-835">Hosts allowed.</span></span> |
| <span data-ttu-id="06cbd-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="06cbd-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="06cbd-837">HTTP 요청을 완료하는 데 걸린 시간과 시작 시간.</span><span class="sxs-lookup"><span data-stu-id="06cbd-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="06cbd-838">로드된 호스팅 시작 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="06cbd-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="06cbd-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="06cbd-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="06cbd-840">MVC 및 Razor 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="06cbd-841">모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택.</span><span class="sxs-lookup"><span data-stu-id="06cbd-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="06cbd-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="06cbd-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="06cbd-843">경로 일치 정보.</span><span class="sxs-lookup"><span data-stu-id="06cbd-843">Route matching information.</span></span> |
| <span data-ttu-id="06cbd-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="06cbd-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="06cbd-845">연결 시작, 중지 및 활성 응답 유지.</span><span class="sxs-lookup"><span data-stu-id="06cbd-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="06cbd-846">HTTPS 인증서 정보.</span><span class="sxs-lookup"><span data-stu-id="06cbd-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="06cbd-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="06cbd-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="06cbd-848">제공된 파일.</span><span class="sxs-lookup"><span data-stu-id="06cbd-848">Files served.</span></span> |
| <span data-ttu-id="06cbd-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="06cbd-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="06cbd-850">일반 Entity Framework Core 진단.</span><span class="sxs-lookup"><span data-stu-id="06cbd-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="06cbd-851">데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션.</span><span class="sxs-lookup"><span data-stu-id="06cbd-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="06cbd-852">로그 범위</span><span class="sxs-lookup"><span data-stu-id="06cbd-852">Log scopes</span></span>

 <span data-ttu-id="06cbd-853">*범위*는 논리적 작업 집합을 그룹화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="06cbd-854">이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="06cbd-855">예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="06cbd-856">범위는 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 `IDisposable` 형식이며 삭제될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="06cbd-857">`using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="06cbd-858">다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="06cbd-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="06cbd-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="06cbd-860">범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="06cbd-861">구성에 대한 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="06cbd-862">각 로그 메시지는 범위 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="06cbd-863">기본 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-863">Built-in logging providers</span></span>

<span data-ttu-id="06cbd-864">ASP.NET Core는 다음 공급자를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="06cbd-865">콘솔</span><span class="sxs-lookup"><span data-stu-id="06cbd-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="06cbd-866">디버그</span><span class="sxs-lookup"><span data-stu-id="06cbd-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="06cbd-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="06cbd-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="06cbd-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="06cbd-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="06cbd-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="06cbd-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="06cbd-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="06cbd-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="06cbd-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="06cbd-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="06cbd-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="06cbd-873">ASP.NET Core 모듈을 사용한 표준 출력(stdout) 및 디버그 로깅에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="06cbd-874">콘솔 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-874">Console provider</span></span>

<span data-ttu-id="06cbd-875">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지는 콘솔에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="06cbd-876">콘솔 로깅 출력을 보려면 프로젝트 폴더에서 명령 프롬프트를 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="06cbd-877">디버그 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-877">Debug provider</span></span>

<span data-ttu-id="06cbd-878">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스(`Debug.WriteLine` 메서드 호출)를 사용하여 로그 출력을 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="06cbd-879">Linux에서 이 공급자는 */var/log/message*에 로그를 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="06cbd-880">이벤트 원본 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-880">Event Source provider</span></span>

<span data-ttu-id="06cbd-881">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지는 `Microsoft-Extensions-Logging` 이름을 사용하여 플랫폼 간 이벤트 원본에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="06cbd-882">Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="06cbd-883">호스트를 빌드하기 위해 `CreateDefaultBuilder`가 호출되면 이벤트 원본 공급자가 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="06cbd-884">[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="06cbd-885">ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="06cbd-886">이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="06cbd-887">(문자열의 시작 부분에 별표를 누락하지 마세요.)</span><span class="sxs-lookup"><span data-stu-id="06cbd-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview 추가 공급자](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="06cbd-889">Windows EventLog 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-889">Windows EventLog provider</span></span>

<span data-ttu-id="06cbd-890">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="06cbd-891">[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)를 사용하여 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="06cbd-892">`null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="06cbd-893">`LogName` - “애플리케이션”</span><span class="sxs-lookup"><span data-stu-id="06cbd-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="06cbd-894">`SourceName` - “.NET 런타임”</span><span class="sxs-lookup"><span data-stu-id="06cbd-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="06cbd-895">`MachineName` - 로컬 머신</span><span class="sxs-lookup"><span data-stu-id="06cbd-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="06cbd-896">[경고 수준 이상](#log-level)의 이벤트가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="06cbd-897">`Warning` 수준보다 낮은 이벤트를 기록하려면 로그 수준을 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="06cbd-898">예를 들어 *appsettings.json* 파일에 다음 내용을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="06cbd-899">TraceSource 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-899">TraceSource provider</span></span>

<span data-ttu-id="06cbd-900">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지는 <xref:System.Diagnostics.TraceSource> 라이브러리 및 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="06cbd-901">[AddTraceSource 오버로드](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)를 사용하여 원본 스위치 및 추적 수신기를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="06cbd-902">이 공급자를 사용하려면 앱이 .NET Framework(.NET Core가 아닌)에서 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="06cbd-903">공급자는 샘플 앱에 사용된 <xref:System.Diagnostics.TextWriterTraceListener>와 같은 다양한 [수신기](/dotnet/framework/debug-trace-profile/trace-listeners)로 메시지를 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="06cbd-904">Azure App Service 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-904">Azure App Service provider</span></span>

<span data-ttu-id="06cbd-905">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="06cbd-906">이 공급자 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="06cbd-907">.NET Framework를 대상으로 지정하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 경우 공급자 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="06cbd-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 오버로드로 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="06cbd-909">설정 개체는 로깅 출력 템플릿, BLOB 이름, 파일 크기 제한과 같은 기본 설정을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="06cbd-910">(*출력 템플릿*은 `ILogger` 메서드를 호출과 함께 제공되는 것 이외에 모든 로그에 적용되는 메시지 템플릿입니다.)</span><span class="sxs-lookup"><span data-stu-id="06cbd-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="06cbd-911">App Service 앱에 배포할 때 응용 프로그램은 Azure 포털 **App Service** 페이지의 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) 섹션에 있는 설정을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="06cbd-912">다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="06cbd-913">**애플리케이션 로깅(파일 시스템)**</span><span class="sxs-lookup"><span data-stu-id="06cbd-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="06cbd-914">**애플리케이션 로깅(Blob)**</span><span class="sxs-lookup"><span data-stu-id="06cbd-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="06cbd-915">로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="06cbd-916">기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="06cbd-917">기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="06cbd-918">공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="06cbd-919">프로젝트를 로컬로 실행하는 경우에는 아무 영향도 없습니다&mdash;로컬 파일 또는 로컬 개발 스토리지에 Blob을 기록하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="06cbd-920">Azure 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="06cbd-920">Azure log streaming</span></span>

<span data-ttu-id="06cbd-921">Azure 로그 스트리밍을 통해 로그 작업을 실시간으로 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="06cbd-922">앱 서버</span><span class="sxs-lookup"><span data-stu-id="06cbd-922">The app server</span></span>
* <span data-ttu-id="06cbd-923">웹 서버</span><span class="sxs-lookup"><span data-stu-id="06cbd-923">The web server</span></span>
* <span data-ttu-id="06cbd-924">실패한 요청 추적</span><span class="sxs-lookup"><span data-stu-id="06cbd-924">Failed request tracing</span></span>

<span data-ttu-id="06cbd-925">Azure 로그 스트리밍을 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="06cbd-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="06cbd-926">앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="06cbd-927">**애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="06cbd-928">로그 **수준**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-928">Choose the log **Level**.</span></span> <span data-ttu-id="06cbd-929">이 설정은 앱의 다른 로깅 공급자가 아닌 Azure 로그 스트리밍에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="06cbd-930">**로그 스트림** 페이지로 이동하여 앱 메시지를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="06cbd-931">앱이 `ILogger` 인터페이스를 통해 기록한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="06cbd-932">Azure Application Insights 추적 로깅</span><span class="sxs-lookup"><span data-stu-id="06cbd-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="06cbd-933">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 Azure Application Insights에 로그를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="06cbd-934">Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="06cbd-935">이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="06cbd-936">로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="06cbd-937">이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="06cbd-938">ASP.NET 4.x용으로 제공되는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지&mdash;를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="06cbd-939">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="06cbd-940">Application Insights 개요</span><span class="sxs-lookup"><span data-stu-id="06cbd-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="06cbd-941">[ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="06cbd-942">[.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="06cbd-943">[Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="06cbd-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="06cbd-944">[Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="06cbd-945">타사 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="06cbd-945">Third-party logging providers</span></span>

<span data-ttu-id="06cbd-946">ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="06cbd-947">[elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="06cbd-949">[JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="06cbd-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="06cbd-950">[KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="06cbd-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="06cbd-951">[Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="06cbd-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="06cbd-952">[Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-953">[NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="06cbd-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="06cbd-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="06cbd-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="06cbd-955">[Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="06cbd-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="06cbd-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="06cbd-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="06cbd-957">일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="06cbd-958">타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="06cbd-959">프로젝트에 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="06cbd-960">로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="06cbd-961">자세한 내용은 각 공급자의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="06cbd-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="06cbd-962">타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="06cbd-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06cbd-963">추가 자료</span><span class="sxs-lookup"><span data-stu-id="06cbd-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
