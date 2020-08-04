---
title: .NET Core 및 ASP.NET Core의 로깅
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet 패키지에서 제공하는 로깅 프레임워크 사용법을 알아보세요.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330773"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="d98e0-103">.NET Core 및 ASP.NET Core의 로깅</span><span class="sxs-lookup"><span data-stu-id="d98e0-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d98e0-104">작성자: [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d98e0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d98e0-105">.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d98e0-106">이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d98e0-107">이 문서에 사용된 대부분의 코드 예제는 ASP.NET Core 앱에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="d98e0-108">해당 코드 조각의 로깅 관련 부분은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 모든 .NET Core 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="d98e0-109">ASP.NET Core 웹앱 템플릿은 제네릭 호스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="d98e0-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d98e0-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="d98e0-111">로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-111">Logging providers</span></span>

<span data-ttu-id="d98e0-112">로그를 표시하는 `Console` 공급자를 제외하고 로깅 공급자는 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="d98e0-113">예를 들어 Azure Application Insights 공급자는 Azure Application Insights의 로그를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="d98e0-114">여러 공급자를 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="d98e0-115">기본 ASP.NET Core 웹앱 템플릿은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="d98e0-116">[제네릭 호스트](xref:fundamentals/host/generic-host)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="d98e0-117">다음 로깅 공급자를 추가하는 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="d98e0-118">콘솔</span><span class="sxs-lookup"><span data-stu-id="d98e0-118">Console</span></span>](#console)
  * [<span data-ttu-id="d98e0-119">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="d98e0-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="d98e0-121">[EventLog](#welog): Windows에만 해당</span><span class="sxs-lookup"><span data-stu-id="d98e0-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="d98e0-122">위의 코드는 ASP.NET Core 웹앱 템플릿을 사용하여 만든 `Program` 클래스를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="d98e0-123">다음 몇 개 섹션에서는 제네릭 호스트를 사용하는 ASP.NET Core 웹앱 템플릿을 기반으로 하는 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="d98e0-124">[비호스트 콘솔 앱](#nhca)은 문서의 뒷부분에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="d98e0-125">`Host.CreateDefaultBuilder`로 추가된 로깅 공급자의 기본 세트를 재정의하려면 `ClearProviders`를 호출하고 필요한 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="d98e0-126">예를 들어, 다음 코드는</span><span class="sxs-lookup"><span data-stu-id="d98e0-126">For example, the following code:</span></span>

* <span data-ttu-id="d98e0-127"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하여 작성기에서 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 인스턴스를 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="d98e0-128">[Console](#console) 로깅 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="d98e0-129">추가 공급자는 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-129">For additional providers, see:</span></span>

* [<span data-ttu-id="d98e0-130">기본 제공 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="d98e0-131">[타사 로깅 공급자](#third-party-logging-providers)</span><span class="sxs-lookup"><span data-stu-id="d98e0-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="d98e0-132">로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-132">Create logs</span></span>

<span data-ttu-id="d98e0-133">로그를 만들려면 [DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d98e0-134">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="d98e0-134">The following example:</span></span>

* <span data-ttu-id="d98e0-135">형식이 `AboutModel`인 정규화된 이름의 로그 ‘범주’를 사용하는 `ILogger<AboutModel>` 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="d98e0-136">로그 범주는 각 로그와 연결된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="d98e0-137"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>을 호출하여 `Information` 수준에 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="d98e0-138">로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="d98e0-139">[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="d98e0-140">Blazor에 대한 자세한 내용은 이 문서의 [Blazor 및 Blazor WebAssembly에서 로그 만들기](#clib)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="d98e0-141">[Main 및 Startup에서 로그 만들기](#clms)에서는 `Main` 및 `Startup`에서 로그를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="d98e0-142">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="d98e0-142">Configure logging</span></span>

<span data-ttu-id="d98e0-143">로깅 구성은 일반적으로 *appsettings*.`{Environment}` *.json* 파일의 `Logging` 섹션에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="d98e0-144">다음 *appsettings.Development.json* 파일은 ASP.NET Core 웹앱 템플릿으로 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="d98e0-145">앞의 JSON에서:</span><span class="sxs-lookup"><span data-stu-id="d98e0-145">In the preceding JSON:</span></span>

* <span data-ttu-id="d98e0-146">`"Default"`, `"Microsoft"` 및 `"Microsoft.Hosting.Lifetime"` 범주가 지정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="d98e0-147">`"Microsoft"` 범주는 `"Microsoft"`로 시작하는 모든 범주에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="d98e0-148">예를 들어 이 설정은 `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` 범주에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="d98e0-149">`"Microsoft"` 범주는 로그 수준 `Warning` 이상에 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="d98e0-150">`"Microsoft.Hosting.Lifetime"` 범주는 `"Microsoft"` 범주보다 구체적이므로 `"Microsoft.Hosting.Lifetime"` 범주는 로그 수준 “정보” 이상에 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="d98e0-151">특정 로그 공급자를 지정하지 않았으므로 `LogLevel`은 [Windows EventLog](#welog)를 제외하고 사용하도록 설정한 모든 로깅 공급자에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="d98e0-152">`Logging` 속성은 <xref:Microsoft.Extensions.Logging.LogLevel> 및 로그 공급자 속성을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="d98e0-153">`LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d98e0-154">위의 JSON에서는 `Information` 및 `Warning` 로그 수준을 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="d98e0-155">`LogLevel` 로그의 심각도를 나타내며 0에서 6 사이의 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="d98e0-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 및 `None` = 6</span><span class="sxs-lookup"><span data-stu-id="d98e0-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="d98e0-157">`LogLevel`을 지정하면 지정된 수준 이상의 메시지에 대해 로깅을 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="d98e0-158">위의 JSON에서 `Default` 범주는 `Information` 이상에 대해 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="d98e0-159">예를 들어 `Information`, `Warning`, `Error` 및 `Critical` 메시지가 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="d98e0-160">`LogLevel` 지정하지 않으면 로깅은 `Information` 수준으로 기본 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="d98e0-161">자세한 내용은 [로그 수준](#llvl)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="d98e0-162">공급자 속성에서 `LogLevel` 속성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="d98e0-163">공급자 아래의 `LogLevel`은 해당 공급자에 대해 로그할 수준을 지정하고 공급자 이외의 로그 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="d98e0-164">다음 *appsettings.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="d98e0-165">`Logging.{providername}.LogLevel`의 설정은 `Logging.LogLevel`의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="d98e0-166">위의 JSON에서는 `Debug` 공급자의 기본 로그 수준이 `Information`로 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="d98e0-167">위 설정은 `Microsoft.Hosting`을 제외하고 모든 `Logging:Debug:` 범주에 대해 `Information` 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="d98e0-168">특정 범주를 나열하면 특정 범주로 기본 범주가 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="d98e0-169">위의 JSON에서 `Logging:Debug:LogLevel` 범주 `"Microsoft.Hosting"` 및 `"Default"`는 `Logging:LogLevel`의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="d98e0-170">최소 로그 수준은 다음에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="d98e0-171">특정 공급자:  예를 들면 `Logging:EventSource:LogLevel:Default:Information`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="d98e0-172">특정 범주: 예를 들면 `Logging:LogLevel:Microsoft:Warning`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="d98e0-173">모든 공급자와 모든 범주: `Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="d98e0-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="d98e0-174">최소 수준 이하의 모든 로그는 다음과 같이 ***되지 않습니다***.</span><span class="sxs-lookup"><span data-stu-id="d98e0-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="d98e0-175">공급자에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-175">Passed to the provider.</span></span>
* <span data-ttu-id="d98e0-176">로그되거나 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-176">Logged or displayed.</span></span>

<span data-ttu-id="d98e0-177">모든 로그를 표시하지 않으려면 [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="d98e0-178">`LogLevel.None`의 값은 `LogLevel.Critical`(5)보다 높은 6입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="d98e0-179">공급자가 [로그 범위](#logscopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d98e0-180">자세한 내용은 [로그 범위](#logscopes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="d98e0-181">다음 *appsettings.json* 파일에는 기본적으로 사용하도록 설정되는 모든 공급자가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="d98e0-182">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="d98e0-182">In the preceding sample:</span></span>

* <span data-ttu-id="d98e0-183">범주 및 수준은 제안된 값이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="d98e0-184">기본 공급자를 모두 표시하기 위해 제공되는 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="d98e0-185">`Logging.{providername}.LogLevel`의 설정은 `Logging.LogLevel`의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="d98e0-186">예를 들어 `Debug.LogLevel.Default` 수준은 `LogLevel.Default`수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="d98e0-187">각 기본 공급자 ‘별칭’을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="d98e0-188">각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="d98e0-189">기본 제공 공급자 별칭은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="d98e0-190">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-190">Console</span></span>
  * <span data-ttu-id="d98e0-191">Debug</span><span class="sxs-lookup"><span data-stu-id="d98e0-191">Debug</span></span>
  * <span data-ttu-id="d98e0-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-192">EventSource</span></span>
  * <span data-ttu-id="d98e0-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="d98e0-193">EventLog</span></span>
  * <span data-ttu-id="d98e0-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d98e0-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="d98e0-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d98e0-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="d98e0-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d98e0-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="d98e0-197">명령줄, 환경 변수 및 기타 구성으로 로그 수준 설정</span><span class="sxs-lookup"><span data-stu-id="d98e0-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="d98e0-198">로그 수준은 [구성 공급자](xref:fundamentals/configuration/index) 중 하나로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d98e0-199">다음 명령은,</span><span class="sxs-lookup"><span data-stu-id="d98e0-199">The following commands:</span></span>

* <span data-ttu-id="d98e0-200">Windows에서 환경 키 `Logging:LogLevel:Microsoft`를 `Information` 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="d98e0-201">ASP.NET Core 웹 애플리케이션 템플릿을 사용하여 만든 앱을 사용할 때 설정을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="d98e0-202">`dotnet run` 명령은 `set`을 사용한 후 프로젝트 디렉터리에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="d98e0-203">위의 환경 설정은,</span><span class="sxs-lookup"><span data-stu-id="d98e0-203">The preceding environment setting:</span></span>

* <span data-ttu-id="d98e0-204">설정된 명령 창에서 시작된 프로세스에서만 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d98e0-205">Visual Studio에서 시작된 브라우저에서는 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d98e0-206">다음 [setx](/windows-server/administration/windows-commands/setx) 명령은 Windows에서 환경 키 및 값도 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="d98e0-207">`set`와 달리, `setx` 설정은 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d98e0-208">`/M` 스위치는 시스템 환경에서 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="d98e0-209">`/M`을 사용하지 않으면 사용자 환경 변수가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="d98e0-210">[Azure App Service](https://azure.microsoft.com/services/app-service/)의 **설정 > 구성** 페이지에서 **새 애플리케이션 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d98e0-211">Azure App Service 애플리케이션 설정은,</span><span class="sxs-lookup"><span data-stu-id="d98e0-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d98e0-212">미사용 시 암호화되고 암호화된 채널을 통해 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d98e0-213">환경 변수로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-213">Exposed as environment variables.</span></span>

<span data-ttu-id="d98e0-214">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d98e0-215">환경 변수를 사용하여 ASP.NET Core 구성 값을 설정하는 방법에 대한 자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="d98e0-216">명령줄, Azure Key Vault, Azure App Configuration, 기타 파일 형식 등을 포함하여 다른 구성 원본을 사용하는 방법에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d98e0-217">필터링 규칙 적용 방식</span><span class="sxs-lookup"><span data-stu-id="d98e0-217">How filtering rules are applied</span></span>

<span data-ttu-id="d98e0-218"><xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 만들 때 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d98e0-219">`ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d98e0-220">사용 가능한 규칙 중에서 각 공급자 및 범주 쌍과 가장 관련이 많은 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d98e0-221">지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d98e0-222">공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d98e0-223">일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d98e0-224">앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d98e0-225">일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d98e0-226">여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d98e0-227">규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="d98e0-228">dotnet run 및 Visual Studio의 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="d98e0-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="d98e0-229">[기본 로깅 공급자](#lp)를 사용하여 만든 로그는 다음과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="d98e0-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d98e0-230">In Visual Studio</span></span>
  * <span data-ttu-id="d98e0-231">디버그할 때 디버그 출력 창에 표시됨</span><span class="sxs-lookup"><span data-stu-id="d98e0-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="d98e0-232">ASP.NET Core 웹 서버 창에 표시됨</span><span class="sxs-lookup"><span data-stu-id="d98e0-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="d98e0-233">`dotnet run`을 사용하여 앱을 실행할 때 콘솔 창에 표시됨</span><span class="sxs-lookup"><span data-stu-id="d98e0-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="d98e0-234">“Microsoft” 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d98e0-235">ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="d98e0-236">로그 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-236">Log category</span></span>

<span data-ttu-id="d98e0-237">`ILogger` 개체가 생성되면 ‘범주’가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="d98e0-238">해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d98e0-239">범주 문자열은 임의로 지정되지만 규칙은 클래스 이름을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="d98e0-240">예를 들어 컨트롤러에서 이름은 `"TodoApi.Controllers.TodoController"`일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="d98e0-241">ASP.NET Core 웹앱은 `ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 자동으로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="d98e0-242">범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="d98e0-243">`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="d98e0-244">로그 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-244">Log level</span></span>

<span data-ttu-id="d98e0-245">다음 표에서는 <xref:Microsoft.Extensions.Logging.LogLevel> 값, 편리한 `Log{LogLevel}` 확장 메서드 및 추천 사용법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="d98e0-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d98e0-246">LogLevel</span></span> | <span data-ttu-id="d98e0-247">값</span><span class="sxs-lookup"><span data-stu-id="d98e0-247">Value</span></span> | <span data-ttu-id="d98e0-248">메서드</span><span class="sxs-lookup"><span data-stu-id="d98e0-248">Method</span></span> | <span data-ttu-id="d98e0-249">Description</span><span class="sxs-lookup"><span data-stu-id="d98e0-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="d98e0-250">추적</span><span class="sxs-lookup"><span data-stu-id="d98e0-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-251">0</span><span class="sxs-lookup"><span data-stu-id="d98e0-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="d98e0-252">가장 자세한 메시지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-252">Contain the most detailed messages.</span></span> <span data-ttu-id="d98e0-253">해당 메시지는 중요한 앱 데이터를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="d98e0-254">해당 메시지는 기본적으로 사용하지 않도록 설정되며 프로덕션에서 사용하도록 설정하면 ***안 됩니다***.</span><span class="sxs-lookup"><span data-stu-id="d98e0-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="d98e0-255">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-256">1</span><span class="sxs-lookup"><span data-stu-id="d98e0-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="d98e0-257">디버깅 및 개발을 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-257">For debugging and development.</span></span> <span data-ttu-id="d98e0-258">너무 많으므로 프로덕션에서는 주의해서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="d98e0-259">정보</span><span class="sxs-lookup"><span data-stu-id="d98e0-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-260">2</span><span class="sxs-lookup"><span data-stu-id="d98e0-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="d98e0-261">앱의 일반적인 흐름을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="d98e0-262">장기적인 값이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-262">May have long-term value.</span></span> |
| [<span data-ttu-id="d98e0-263">경고</span><span class="sxs-lookup"><span data-stu-id="d98e0-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-264">3</span><span class="sxs-lookup"><span data-stu-id="d98e0-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="d98e0-265">비정상적이거나 예기치 않은 이벤트를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="d98e0-266">일반적으로 앱의 오류를 발생시키지 않는 오류 또는 조건을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="d98e0-267">오류</span><span class="sxs-lookup"><span data-stu-id="d98e0-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-268">4</span><span class="sxs-lookup"><span data-stu-id="d98e0-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="d98e0-269">처리할 수 없는 오류 및 예외를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d98e0-270">해당 메시지는 전체 앱 오류가 아닌 현재 작업 또는 요청의 오류를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="d98e0-271">심각</span><span class="sxs-lookup"><span data-stu-id="d98e0-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-272">5</span><span class="sxs-lookup"><span data-stu-id="d98e0-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="d98e0-273">즉각적인 대응이 필요한 오류를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-273">For failures that require immediate attention.</span></span> <span data-ttu-id="d98e0-274">예: 데이터 손실 시나리오, 디스크 공간 부족.</span><span class="sxs-lookup"><span data-stu-id="d98e0-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="d98e0-275">없음</span><span class="sxs-lookup"><span data-stu-id="d98e0-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="d98e0-276">6</span><span class="sxs-lookup"><span data-stu-id="d98e0-276">6</span></span> | | <span data-ttu-id="d98e0-277">로깅 범주에서 메시지를 쓰지 않도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="d98e0-278">위의 표에서는 `LogLevel`이 심각도가 낮은 것에서 높은 것 순으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="d98e0-279">[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) 메서드의 첫 번째 매개 변수인 <xref:Microsoft.Extensions.Logging.LogLevel>은 로그의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="d98e0-280">대부분의 개발자는 `Log(LogLevel, ...)` 대신 [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="d98e0-281">`Log{LogLevel}` 확장 메서드는 [Log 메서드를 호출하고 LogLevel을 지정](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="d98e0-282">예를 들어 다음 두 로깅 호출은 기능이 동일하며 같은 로그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="d98e0-283">`MyLogEvents.TestItem`은 이벤트 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="d98e0-284">`MyLogEvents`는 샘플 앱의 일부이며 [로그 이벤트 ID](#leid) 섹션에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="d98e0-285">다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d98e0-286">위의 코드에서 첫 번째 `Log{LogLevel}` 매개 변수 `MyLogEvents.GetItem`은 [로그 이벤트 ID](#leid)입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="d98e0-287">두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d98e0-288">메서드 매개 변수는 이 문서의 뒷부분에 있는 [메시지 템플릿](#lmt) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="d98e0-289">적절한 `Log{LogLevel}` 메서드를 호출하여 특정 스토리지 매체에 기록되는 로그 출력의 양을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="d98e0-290">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d98e0-290">For example:</span></span>

* <span data-ttu-id="d98e0-291">프로덕션:</span><span class="sxs-lookup"><span data-stu-id="d98e0-291">In production:</span></span>
  * <span data-ttu-id="d98e0-292">`Trace` 또는 `Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d98e0-293">비용을 관리하고 데이터 스토리지 제한을 초과하지 않으려면 `Trace` 및 `Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="d98e0-294">`Trace` 및 `Information`을 특정 범주로 제한하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="d98e0-295">`Warning`~`Critical` 수준의 로깅은 적은 로그 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="d98e0-296">비용과 스토리지 제한이 일반적으로 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="d98e0-297">로그가 적으므로 데이터 저장소를 더 유연하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="d98e0-298">개발 중:</span><span class="sxs-lookup"><span data-stu-id="d98e0-298">In development:</span></span>
  * <span data-ttu-id="d98e0-299">`Warning`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="d98e0-300">문제를 해결할 때는 `Trace` 또는 `Information` 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="d98e0-301">출력을 제한하려면 조사 중인 범주에 대해서만 `Trace` 또는 `Information`을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="d98e0-302">ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d98e0-303">예를 들어 다음의 로그 출력을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="d98e0-304">ASP.NET Core 템플릿을 사용하여 만든 Razor Pages 앱</span><span class="sxs-lookup"><span data-stu-id="d98e0-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="d98e0-305">로깅을 `Logging:Console:LogLevel:Microsoft:Information`으로 설정</span><span class="sxs-lookup"><span data-stu-id="d98e0-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="d98e0-306">개인정보처리방침 페이지로 이동</span><span class="sxs-lookup"><span data-stu-id="d98e0-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="d98e0-307">다음 JSON에서는 `Logging:Console:LogLevel:Microsoft:Information`을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="d98e0-308">로그 이벤트 ID</span><span class="sxs-lookup"><span data-stu-id="d98e0-308">Log event ID</span></span>

<span data-ttu-id="d98e0-309">각 로그는 *이벤트 ID*를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d98e0-310">샘플 앱에서는 `MyLogEvents` 클래스를 사용하여 이벤트 ID를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d98e0-311">이벤트 ID는 이벤트 집합을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-311">An event ID associates a set of events.</span></span> <span data-ttu-id="d98e0-312">예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d98e0-313">로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d98e0-314">디버그 공급자는 이벤트 ID를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d98e0-315">콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="d98e0-316">일부 로깅 공급자는 ID에 대한 필터링을 허용하는 필드에 이벤트 ID를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="d98e0-317">로그 메시지 템플릿</span><span class="sxs-lookup"><span data-stu-id="d98e0-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="d98e0-318">각 로그 API는 메시지 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-318">Each log API uses a message template.</span></span> <span data-ttu-id="d98e0-319">메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d98e0-320">번호가 아닌 자리 표시자의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="d98e0-321">값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d98e0-322">다음 코드에서는 매개 변수 이름이 메시지 템플릿의 순서와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d98e0-323">앞의 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="d98e0-324">해당 접근 방식을 통해 로깅 공급자는 [의미 체계 또는 구조적 로깅](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="d98e0-325">인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d98e0-326">따라서 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d98e0-327">예를 들어 다음 로거 메서드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d98e0-328">Azure Table Storage에 로그할 경우를 예로 듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="d98e0-329">각 Azure Table 엔터티에는 `ID` 및 `RequestTime` 속성이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="d98e0-330">속성이 있는 테이블은 로그된 데이터의 쿼리를 쉽게 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="d98e0-331">예를 들어 문자 메시지의 시간 초과를 구문 분석하지 않고도 특정 `RequestTime` 범위 내에 있는 모든 로그를 쿼리를 통해 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="d98e0-332">예외 로그</span><span class="sxs-lookup"><span data-stu-id="d98e0-332">Log exceptions</span></span>

<span data-ttu-id="d98e0-333">로거 메서드에는 예외 매개 변수를 사용하는 오버로드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="d98e0-334">예외 로깅은 공급자별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="d98e0-335">기본 로그 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-335">Default log level</span></span>

<span data-ttu-id="d98e0-336">기본 로그 수준을 설정하지 않으면 기본 로그 수준 값은 `Information`입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="d98e0-337">예를 들어 다음 웹앱을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="d98e0-338">ASP.NET 웹앱 템플릿을 사용하여 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="d98e0-339">*appsettings.json* 및 *appsettings.Development.json*을 삭제하거나 이름을 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="d98e0-340">이전 설정을 사용하여 개인정보처리방침이나 홈페이지로 이동하면 범주 이름에 `Microsoft`가 포함된 `Trace`, `Debug`및 `Information` 메시지가 많이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="d98e0-341">다음 코드에서는 구성에 기본 로그 수준이 설정되어 있지 않은 경우 기본 로그 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="d98e0-342">일반적으로 로그 수준은 코드에서가 아니라 구성에서 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="d98e0-343">필터 함수</span><span class="sxs-lookup"><span data-stu-id="d98e0-343">Filter function</span></span>

<span data-ttu-id="d98e0-344">필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="d98e0-345">앞의 코드는 범주에 `Controller` 또는 `Microsoft`가 포함되어 있고 로그 수준이 `Information` 이상인 경우 콘솔 로그를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="d98e0-346">일반적으로 로그 수준은 코드에서가 아니라 구성에서 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="d98e0-347">ASP.NET Core 및 EF Core 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="d98e0-348">다음 표에는 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주와 로그에 관한 참고 사항이 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="d98e0-349">범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-349">Category</span></span>                            | <span data-ttu-id="d98e0-350">참고</span><span class="sxs-lookup"><span data-stu-id="d98e0-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d98e0-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d98e0-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d98e0-352">일반 ASP.NET Core 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d98e0-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d98e0-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d98e0-354">고려되고, 발견되고, 사용된 키.</span><span class="sxs-lookup"><span data-stu-id="d98e0-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d98e0-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d98e0-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d98e0-356">허용되는 호스트.</span><span class="sxs-lookup"><span data-stu-id="d98e0-356">Hosts allowed.</span></span> |
| <span data-ttu-id="d98e0-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d98e0-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d98e0-358">HTTP 요청을 완료하는 데 걸린 시간과 시작 시간.</span><span class="sxs-lookup"><span data-stu-id="d98e0-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d98e0-359">로드된 호스팅 시작 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="d98e0-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d98e0-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d98e0-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d98e0-361">MVC 및 Razor 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d98e0-362">모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택.</span><span class="sxs-lookup"><span data-stu-id="d98e0-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d98e0-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d98e0-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d98e0-364">경로 일치 정보.</span><span class="sxs-lookup"><span data-stu-id="d98e0-364">Route matching information.</span></span> |
| <span data-ttu-id="d98e0-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="d98e0-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d98e0-366">연결 시작, 중지 및 활성 응답 유지.</span><span class="sxs-lookup"><span data-stu-id="d98e0-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d98e0-367">HTTPS 인증서 정보.</span><span class="sxs-lookup"><span data-stu-id="d98e0-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d98e0-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d98e0-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d98e0-369">제공된 파일.</span><span class="sxs-lookup"><span data-stu-id="d98e0-369">Files served.</span></span> |
| <span data-ttu-id="d98e0-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d98e0-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d98e0-371">일반 Entity Framework Core 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d98e0-372">데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션.</span><span class="sxs-lookup"><span data-stu-id="d98e0-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="d98e0-373">콘솔 창에서 더 많은 범주를 보려면 **appsettings.Development.json**을 다음으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="d98e0-374">로그 범위</span><span class="sxs-lookup"><span data-stu-id="d98e0-374">Log scopes</span></span>

 <span data-ttu-id="d98e0-375">*범위*는 논리적 작업 집합을 그룹화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d98e0-376">이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d98e0-377">예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d98e0-378">범위:</span><span class="sxs-lookup"><span data-stu-id="d98e0-378">A scope:</span></span>

* <span data-ttu-id="d98e0-379"><xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 <xref:System.IDisposable> 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="d98e0-380">삭제될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="d98e0-381">범위를 지원하는 공급자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="d98e0-382">AzureAppServicesFile 및 AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d98e0-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="d98e0-383">`using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="d98e0-384">다음 JSON은 콘솔 공급자에 대해 범위를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="d98e0-385">다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="d98e0-386">일반적으로 로깅은 코드에서가 아니라 구성에서 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="d98e0-387">기본 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-387">Built-in logging providers</span></span>

<span data-ttu-id="d98e0-388">ASP.NET Core에는 다음과 같은 로깅 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="d98e0-389">콘솔</span><span class="sxs-lookup"><span data-stu-id="d98e0-389">Console</span></span>](#console)
* [<span data-ttu-id="d98e0-390">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-390">Debug</span></span>](#debug)
* [<span data-ttu-id="d98e0-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="d98e0-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="d98e0-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="d98e0-393">AzureAppServicesFile 및 AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d98e0-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="d98e0-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d98e0-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="d98e0-395">ASP.NET Core 모듈을 사용한 `stdout` 및 디버그 로깅에 대한 자세한 내용은 및 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="d98e0-396">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-396">Console</span></span>

<span data-ttu-id="d98e0-397">`Console` 공급자는 콘솔에 출력을 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="d98e0-398">개발 중인 `Console` 로그를 보는 방법에 대한 자세한 내용은 [dotnet run 및 Visual Studio의 로깅 출력](#dnrvs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="d98e0-399">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-399">Debug</span></span>

<span data-ttu-id="d98e0-400">`Debug` 공급자는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스를 사용하여 로그 출력을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="d98e0-401">`System.Diagnostics.Debug.WriteLine`을 호출하여 `Debug` 공급자에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="d98e0-402">Linux에서 `Debug` 공급자 로그 위치는 배포판마다 다르며 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="d98e0-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="d98e0-403">*/var/log/message*</span></span>
* <span data-ttu-id="d98e0-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="d98e0-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="d98e0-405">이벤트 원본</span><span class="sxs-lookup"><span data-stu-id="d98e0-405">Event Source</span></span>

<span data-ttu-id="d98e0-406">`EventSource` 공급자는 이름이 `Microsoft-Extensions-Logging`인 플랫폼 간 이벤트 원본에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d98e0-407">Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="d98e0-408">dotnet 추적 도구</span><span class="sxs-lookup"><span data-stu-id="d98e0-408">dotnet trace tooling</span></span>

<span data-ttu-id="d98e0-409">[dotnet 추적](/dotnet/core/diagnostics/dotnet-trace) 도구는 실행 중인 프로세스의 .NET Core 추적을 수집할 수 있도록 하는 플랫폼 간 CLI 전역 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="d98e0-410">이 도구는 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>를 사용하여 <xref:Microsoft.Extensions.Logging.EventSource> 공급자 데이터를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="d98e0-411">설치 지침은 [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="d98e0-412">Dotnet 추적 도구를 사용하여 앱에서 추적을 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="d98e0-413">`dotnet run` 명령으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="d98e0-414">.NET Core 앱의 PID(프로세스 식별자)를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="d98e0-415">Windows에서 다음 접근 방식 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="d98e0-416">작업 관리자(Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="d98e0-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="d98e0-417">tasklist 명령</span><span class="sxs-lookup"><span data-stu-id="d98e0-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="d98e0-418">Get-Process Powershell 명령</span><span class="sxs-lookup"><span data-stu-id="d98e0-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="d98e0-419">Linux에서는 [pidof 명령](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="d98e0-420">앱의 어셈블리와 동일한 이름의 프로세스에 대한 PID를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="d98e0-421">`dotnet trace` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="d98e0-422">일반 명령 구문:</span><span class="sxs-lookup"><span data-stu-id="d98e0-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="d98e0-423">PowerShell 명령 셸을 사용하는 경우 `--providers` 값을 작은따옴표(`'`)로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="d98e0-424">Windows가 아닌 플랫폼에서는 출력 추적 파일의 형식을 `speedscope`로 변경하는 `-f speedscope` 옵션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="d98e0-425">키워드</span><span class="sxs-lookup"><span data-stu-id="d98e0-425">Keyword</span></span> | <span data-ttu-id="d98e0-426">Description</span><span class="sxs-lookup"><span data-stu-id="d98e0-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="d98e0-427">1</span><span class="sxs-lookup"><span data-stu-id="d98e0-427">1</span></span>       | <span data-ttu-id="d98e0-428">`LoggingEventSource`에 대한 로그 메타 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="d98e0-429">`ILogger`)에서 이벤트를 기록하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="d98e0-430">2</span><span class="sxs-lookup"><span data-stu-id="d98e0-430">2</span></span>       | <span data-ttu-id="d98e0-431">`ILogger.Log()`가 호출될 때 `Message` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d98e0-432">서식 지정되지 않은 프로그래밍 방식으로 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="d98e0-433">4</span><span class="sxs-lookup"><span data-stu-id="d98e0-433">4</span></span>       | <span data-ttu-id="d98e0-434">`ILogger.Log()`가 호출될 때 `FormatMessage` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d98e0-435">서식 지정된 문자열 버전의 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="d98e0-436">8</span><span class="sxs-lookup"><span data-stu-id="d98e0-436">8</span></span>       | <span data-ttu-id="d98e0-437">`ILogger.Log()`가 호출될 때 `MessageJson` 이벤트 설정을 켭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d98e0-438">인수의 JSON 표현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="d98e0-439">이벤트 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-439">Event Level</span></span> | <span data-ttu-id="d98e0-440">설명</span><span class="sxs-lookup"><span data-stu-id="d98e0-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="d98e0-441">0</span><span class="sxs-lookup"><span data-stu-id="d98e0-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="d98e0-442">1</span><span class="sxs-lookup"><span data-stu-id="d98e0-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="d98e0-443">2</span><span class="sxs-lookup"><span data-stu-id="d98e0-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="d98e0-444">3</span><span class="sxs-lookup"><span data-stu-id="d98e0-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="d98e0-445">4</span><span class="sxs-lookup"><span data-stu-id="d98e0-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="d98e0-446">5</span><span class="sxs-lookup"><span data-stu-id="d98e0-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="d98e0-447">`FilterSpecs``{Logger Category}` 및 `{Event Level}`에 대한 항목은 추가 로그 필터링 조건을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="d98e0-448">`FilterSpecs` 항목을 세미콜론(`;`)으로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="d98e0-449">Windows 명령 셸을 사용하는 예(`--providers` 값에 작은따옴표를 사용하지 **않음**):</span><span class="sxs-lookup"><span data-stu-id="d98e0-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="d98e0-450">이전 명령은 다음과 같이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-450">The preceding command activates:</span></span>

   * <span data-ttu-id="d98e0-451">(`2`) 오류에 대해 형식이 지정된 문자열(`4`)을 생성하는 이벤트 원본 로거입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="d98e0-452">`Informational` 로깅 수준에서 `Microsoft.AspNetCore.Hosting` 로깅(`4`).</span><span class="sxs-lookup"><span data-stu-id="d98e0-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="d98e0-453">Enter 키 또는 Ctrl + C를 눌러 dotnet 추적 도구를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="d98e0-454">추적은 `dotnet trace` 명령이 실행되는 폴더에 *trace.nettrace* 이름으로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="d98e0-455">[Perfview](#perfview)를 사용하여 추적을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="d98e0-456">*trace.nettrace* 파일을 열고 추적 이벤트를 탐색합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="d98e0-457">앱이 `CreateDefaultBuilder`를 사용하여 호스트를 빌드하지 않는 경우 [이벤트 원본 공급자](#event-source-provider)를 앱의 로깅 구성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="d98e0-458">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-458">For more information, see:</span></span>

* <span data-ttu-id="d98e0-459">[성능 분석 유틸리티 추적(dotnet 추적)](/dotnet/core/diagnostics/dotnet-trace)(.NET Core 설명서)</span><span class="sxs-lookup"><span data-stu-id="d98e0-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="d98e0-460">[성능 분석 유틸리티 추적(dotnet 추적)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)(dotnet/진단 GitHub 리포지토리 설명서)</span><span class="sxs-lookup"><span data-stu-id="d98e0-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="d98e0-461">[LoggingEventSource 클래스](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)(.NET API 브라우저)</span><span class="sxs-lookup"><span data-stu-id="d98e0-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="d98e0-462">[LoggingEventSource 참조 원본(3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): 다른 버전의 참조 원본을 가져오려면 분기를 `release/{Version}`으로 변경합니다. 여기서 `{Version}`은 원하는 ASP.NET Core 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="d98e0-463">[Perfview](#perfview): 이벤트 원본 추적을 보는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="d98e0-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="d98e0-464">Perfview</span></span>

<span data-ttu-id="d98e0-465">[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d98e0-466">ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d98e0-467">이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d98e0-468">문자열의 시작 부분에 `*`를 누락하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="d98e0-469">Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="d98e0-469">Windows EventLog</span></span>

<span data-ttu-id="d98e0-470">`EventLog` 공급자는 로그 출력을 Windows 이벤트 로그에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="d98e0-471">다른 공급자와 달리 `EventLog` 공급자는 기본 비공급자 설정을 상속하지 ***않습니다***.</span><span class="sxs-lookup"><span data-stu-id="d98e0-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="d98e0-472">`EventLog` 로그 설정을 지정하지 않으면 [LogLevel.Warning으로 기본 설정](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="d98e0-473"><xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 수준보다 낮은 이벤트를 기록하려면 로그 수준을 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="d98e0-474">다음 예제에서는 이벤트 로그 기본 로그 수준을 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="d98e0-475">[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)는 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d98e0-476">`null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d98e0-477">`LogName`: “Application”</span><span class="sxs-lookup"><span data-stu-id="d98e0-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="d98e0-478">`SourceName`: “.NET Runtime”</span><span class="sxs-lookup"><span data-stu-id="d98e0-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="d98e0-479">`MachineName`: 로컬 머신 이름이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="d98e0-480">다음 코드에서는 `SourceName`을 기본값 `".NET Runtime"`에서 `MyLogs`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="d98e0-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d98e0-481">Azure App Service</span></span>

<span data-ttu-id="d98e0-482">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="d98e0-483">이 공급자 패키지는 공유 프레임워크에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="d98e0-484">이 공급자를 사용하려면 프로젝트에 공급자 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="d98e0-485">공급자 설정을 구성하려면 다음 예제와 같이 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 및 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="d98e0-486">Azure App Service에 배포하는 경우 앱은 Azure Portal에서 **App Service** 페이지에 있는 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) 섹션의 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d98e0-487">다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d98e0-488">**애플리케이션 로깅(파일 시스템)**</span><span class="sxs-lookup"><span data-stu-id="d98e0-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d98e0-489">**애플리케이션 로깅(Blob)**</span><span class="sxs-lookup"><span data-stu-id="d98e0-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d98e0-490">로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d98e0-491">기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d98e0-492">기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d98e0-493">이 공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d98e0-494">Azure 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="d98e0-494">Azure log streaming</span></span>

<span data-ttu-id="d98e0-495">Azure 로그 스트리밍을 통해 다음에서 로그 활동을 실시간으로 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="d98e0-496">앱 서버</span><span class="sxs-lookup"><span data-stu-id="d98e0-496">The app server</span></span>
* <span data-ttu-id="d98e0-497">웹 서버</span><span class="sxs-lookup"><span data-stu-id="d98e0-497">The web server</span></span>
* <span data-ttu-id="d98e0-498">실패한 요청 추적</span><span class="sxs-lookup"><span data-stu-id="d98e0-498">Failed request tracing</span></span>

<span data-ttu-id="d98e0-499">Azure 로그 스트리밍을 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="d98e0-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d98e0-500">앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="d98e0-501">**애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d98e0-502">로그 **수준**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-502">Choose the log **Level**.</span></span> <span data-ttu-id="d98e0-503">이 설정은 Azure 로그 스트리밍에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="d98e0-504">**로그 스트림** 페이지로 이동하여 로그를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="d98e0-505">로드되는 메시지는 `ILogger` 인터페이스를 사용하여 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="d98e0-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d98e0-506">Azure Application Insights</span></span>

<span data-ttu-id="d98e0-507">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 [Azure Application Insights](/azure/azure-monitor/app/cloudservices)에 로그를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="d98e0-508">Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d98e0-509">이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d98e0-510">로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d98e0-511">이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d98e0-512">[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지는 ASP.NET Core가 아니라 ASP.NET 4.x용입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="d98e0-513">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d98e0-514">Application Insights 개요</span><span class="sxs-lookup"><span data-stu-id="d98e0-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d98e0-515">[ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d98e0-516">[.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d98e0-517">[Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="d98e0-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d98e0-518">[Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d98e0-519">타사 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-519">Third-party logging providers</span></span>

<span data-ttu-id="d98e0-520">ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d98e0-521">[elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d98e0-523">[JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d98e0-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d98e0-524">[KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d98e0-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d98e0-525">[Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d98e0-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d98e0-526">[Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-527">[NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/)([GitHub 리포지토리](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="d98e0-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="d98e0-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d98e0-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d98e0-530">[Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d98e0-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d98e0-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d98e0-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d98e0-532">일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d98e0-533">타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d98e0-534">프로젝트에 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d98e0-535">로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d98e0-536">자세한 내용은 각 공급자의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d98e0-537">타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="d98e0-538">비호스트 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="d98e0-538">Non-host console app</span></span>

<span data-ttu-id="d98e0-539">비 웹 콘솔 앱에서 일반 호스트를 사용하는 방법에 대한 사례는 [백그라운드 작업 샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples)(<xref:fundamentals/host/hosted-services>)의 *Program.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="d98e0-540">제네릭 호스트를 사용하지 않는 앱의 로깅 코드는 [공급자 추가](#add-providers) 및 [로거 생성](#create-logs) 방식에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="d98e0-541">로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-541">Logging providers</span></span>

<span data-ttu-id="d98e0-542">비 호스트 콘솔 앱에서는 `LoggerFactory`를 만드는 동안 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="d98e0-543">로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-543">Create logs</span></span>

<span data-ttu-id="d98e0-544">로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d98e0-545">`LoggerFactory`를 사용하여 `ILogger`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d98e0-546">다음 예제에서는 `LoggingConsoleApp.Program`을 범주로 사용하여 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="d98e0-547">다음의 ASP.NET Core 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d98e0-548">로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="d98e0-549">[수준](#log-level) 및 [범주](#log-category)는 이 문서에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="d98e0-550">호스트 생성 중 로깅</span><span class="sxs-lookup"><span data-stu-id="d98e0-550">Log during host construction</span></span>

<span data-ttu-id="d98e0-551">호스트 생성 중 로깅은 직접 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d98e0-552">그러나 별도의 로거를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-552">However, a separate logger can be used.</span></span> <span data-ttu-id="d98e0-553">다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateHostBuilder`에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="d98e0-554">`AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="d98e0-555">ILogger에 종속되는 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="d98e0-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="d98e0-556">이전 버전의 ASP.NET Core에서는 `Startup`에 대한 로거의 생성자 주입이 작동하는데, 이는 웹 호스트에 대한 별도의 DI 컨테이너가 생성되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="d98e0-557">제네릭 호스트에 대해 하나의 컨테이너만 생성되는 이유에 대한 자세한 내용은 [호환성이 손상되는 변경 공지](https://github.com/aspnet/Announcements/issues/353)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="d98e0-558">`ILogger<T>`에 종속되는 서비스를 구성하려면 생성자 주입을 사용하거나 팩터리 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="d98e0-559">팩터리 메서드 접근 방식은 다른 옵션이 없는 경우에만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="d98e0-560">예를 들어 DI에서 제공하는 `ILogger<T>` 인스턴스가 필요한 서비스를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="d98e0-561">위에 강조 표시된 코드는 DI 컨테이너가 `MyService`의 인스턴스를 처음 생성해야 할 때 실행되는 [Func](/dotnet/api/system.func-2)입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="d98e0-562">이러한 방식으로 등록된 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="d98e0-563">Main에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-563">Create logs in Main</span></span>

<span data-ttu-id="d98e0-564">다음 코드는 호스트를 빌드한 후 DI에서 `ILogger` 인스턴스를 가져와 `Main`에 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="d98e0-565">시작 시 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-565">Create logs in Startup</span></span>

<span data-ttu-id="d98e0-566">다음 코드는 `Startup.Configure`에 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="d98e0-567">`Startup.ConfigureServices` 메서드에서 DI 컨테이너 설정이 완료되기 전에 로그를 작성하는 작업은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="d98e0-568">`Startup` 생성자에 대한 로거 주입은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="d98e0-569">`Startup.ConfigureServices` 메서드 시그니처에 대한 로거 주입은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="d98e0-570">이러한 제한의 이유는 로깅이 DI와 구성에 종속되며, 이는 결국 DI에 종속되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="d98e0-571">DI 컨테이너는 `ConfigureServices`가 완료될 때까지 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="d98e0-572">`ILogger<T>`에 종속되는 서비스를 구성하는 방법이나 `Startup`로 로거의 생성자 주입이 이전 버전에서 작동한 이유에 대한 자세한 내용은 [ILogger에 종속되는 서비스 구성](#csdi)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d98e0-573">비동기 로거 메서드 미지원</span><span class="sxs-lookup"><span data-stu-id="d98e0-573">No asynchronous logger methods</span></span>

<span data-ttu-id="d98e0-574">로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d98e0-575">로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d98e0-576">로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="d98e0-577">예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d98e0-578">대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d98e0-579">자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="d98e0-580">실행 중인 앱에서 로그 수준 변경</span><span class="sxs-lookup"><span data-stu-id="d98e0-580">Change log levels in a running app</span></span>

<span data-ttu-id="d98e0-581">로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d98e0-582">그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d98e0-583">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 기본적으로 로깅 구성을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="d98e0-584">앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="d98e0-585">ILogger 및 ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="d98e0-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="d98e0-586"><xref:Microsoft.Extensions.Logging.ILogger%601> 및 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 인터페이스와 구현은 .NET Core SDK에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="d98e0-587">또한 다음 NuGet 패키지에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="d98e0-588">인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="d98e0-589">기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="d98e0-590">코드에서 로그 필터 규칙 적용</span><span class="sxs-lookup"><span data-stu-id="d98e0-590">Apply log filter rules in code</span></span>

<span data-ttu-id="d98e0-591">로그 필터 규칙을 설정하는 기본 방법은 [구성](xref:fundamentals/configuration/index)을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="d98e0-592">다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="d98e0-593">`logging.AddFilter("System", LogLevel.Debug)`는 `System`범주 및 로그 수준`Debug`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="d98e0-594">특정 공급자를 구성하지 않았으므로 필터가 모든 공급자에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="d98e0-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`은 다음을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="d98e0-596">`Debug` 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="d98e0-597">로그 수준 `Information` 이상</span><span class="sxs-lookup"><span data-stu-id="d98e0-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="d98e0-598">`"Microsoft"`로 시작하는 모든 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="d98e0-599">사용자 지정 로거 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-599">Create a custom logger</span></span>

<span data-ttu-id="d98e0-600">사용자 지정 로거를 추가하려면 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 <xref:Microsoft.Extensions.Logging.ILoggerProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="d98e0-601">`ILoggerProvider`는 하나 이상의 `ILogger` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="d98e0-602">`ILogger` 인스턴스는 프레임워크에서 정보를 로그하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="d98e0-603">샘플 사용자 지정 로거 구성</span><span class="sxs-lookup"><span data-stu-id="d98e0-603">Sample custom logger configuration</span></span>

<span data-ttu-id="d98e0-604">샘플:</span><span class="sxs-lookup"><span data-stu-id="d98e0-604">The sample:</span></span>

* <span data-ttu-id="d98e0-605">이벤트 ID 및 로그 수준으로 로그 콘솔의 색을 설정하는 기본적인 샘플로 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="d98e0-606">로거는 일반적으로 이벤트 ID로 변경되지 않으며 로그 수준에 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="d98e0-607">다음 구성 유형을 사용하여 로그 수준 및 이벤트 ID에 따라 다른 색의 콘솔 항목을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="d98e0-608">위의 코드는 기본 수준을 `Warning`으로 설정하고 색을 `Yellow`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="d98e0-609">`EventId`를 0으로 설정하면 모든 이벤트를 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="d98e0-610">사용자 지정 로거 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-610">Create the custom logger</span></span>

<span data-ttu-id="d98e0-611">`ILogger` 구현 범주 이름은 일반적으로 로깅 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="d98e0-612">예를 들어 다음은 로거를 만드는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="d98e0-613">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="d98e0-613">The preceding code:</span></span>

* <span data-ttu-id="d98e0-614">범주 이름별로 로거 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="d98e0-615">`IsEnabled`에서 `logLevel == _config.LogLevel`을 확인하므로 각 `logLevel`에 고유한 로거가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="d98e0-616">일반적으로 모든 상위 로그 수준에 대해서도 로거를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="d98e0-617">사용자 지정 LoggerProvider 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="d98e0-618">`LoggerProvider`는 로거 인스턴스를 만드는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="d98e0-619">범주별로 로거 인스턴스를 만들 필요가 없을 수도 있지만, NLog 또는 log4net와 같은 일부 로거에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="d98e0-620">이렇게 하면 필요한 경우 범주별로 다른 로깅 출력 대상을 선택할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="d98e0-621">위의 코드에서 <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*>는 범주 이름별로 `ColorConsoleLogger`의 단일 인스턴스를 만들어 [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="d98e0-622">사용자 지정 로거의 사용 및 등록</span><span class="sxs-lookup"><span data-stu-id="d98e0-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="d98e0-623">`Startup.Configure`에 로거를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="d98e0-624">앞의 코드에서는 `ILoggerFactory`를 위한 확장 메서드를 하나 이상 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="d98e0-625">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d98e0-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="d98e0-626">로깅 버그는 [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) 리포지토리에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d98e0-627">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d98e0-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d98e0-628">.NET Core는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d98e0-629">이 문서에서는 기본 제공 공급자를 이용하여 로깅 API를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d98e0-630">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d98e0-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="d98e0-631">공급자 추가</span><span class="sxs-lookup"><span data-stu-id="d98e0-631">Add providers</span></span>

<span data-ttu-id="d98e0-632">로깅 공급자는 로그를 표시하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="d98e0-633">예를 들어 콘솔 공급자는 콘솔에 로그를 표시하고 Azure Application Insights 공급자는 로그를 Azure Application Insights에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="d98e0-634">여러 공급자를 추가하여 로그를 여러 대상으로 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="d98e0-635">공급자를 추가하려면 *Program.cs*에서 공급자의 `Add{provider name}` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="d98e0-636">앞의 코드는 `Microsoft.Extensions.Logging` 및 `Microsoft.Extensions.Configuration`에 대한 참조를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="d98e0-637">기본 프로젝트 템플릿은 다음과 같은 로깅 공급자를 추가하는 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="d98e0-638">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-638">Console</span></span>
* <span data-ttu-id="d98e0-639">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-639">Debug</span></span>
* <span data-ttu-id="d98e0-640">EventSource(ASP.NET Core 2.2부터 시작)</span><span class="sxs-lookup"><span data-stu-id="d98e0-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="d98e0-641">`CreateDefaultBuilder`를 사용하는 경우 기본 제공자를 사용자가 선택한 대로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="d98e0-642"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>를 호출하고 원하는 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="d98e0-643">문서의 뒷부분에 나오는 [기본 제공 로깅 공급자](#built-in-logging-providers) 및 [타사 로깅 공급자](#third-party-logging-providers)에서 더 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="d98e0-644">로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-644">Create logs</span></span>

<span data-ttu-id="d98e0-645">로그를 만들려면 <xref:Microsoft.Extensions.Logging.ILogger%601> 개체를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d98e0-646">웹앱 또는 호스트되는 서비스에서는 DI(종속성 주입)로부터 `ILogger`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="d98e0-647">비 호스트 콘솔 앱에서는 `LoggerFactory`를 사용하여 `ILogger`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d98e0-648">다음 ASP.NET Core 예제는 `TodoApiSample.Pages.AboutModel`을 범주로 사용하여 로거를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="d98e0-649">로그 *범주*는 각 로그와 연결된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="d98e0-650">DI에서 제공한 `ILogger<T>` 인스턴스는 `T` 형식의 정규화된 이름을 범주로 가진 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="d98e0-651">다음의 ASP.NET Core 및 콘솔 앱 예제에서 로거는 `Information`을 수준으로 사용하여 로그를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d98e0-652">로그 *수준*은 기록된 이벤트의 심각도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="d98e0-653">[수준](#log-level) 및 [범주](#log-category)는 이 문서의 뒷부분에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="d98e0-654">시작 시 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-654">Create logs in Startup</span></span>

<span data-ttu-id="d98e0-655">`Startup` 클래스에서 로그를 작성하려면 생성자 시그니처에 `ILogger` 매개 변수를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="d98e0-656">Program 클래스에서 로그 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-656">Create logs in the Program class</span></span>

<span data-ttu-id="d98e0-657">`Program` 클래스에 로그를 작성하려면 DI에서 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="d98e0-658">호스트 생성 중 로깅은 직접 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d98e0-659">그러나 별도의 로거를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-659">However, a separate logger can be used.</span></span> <span data-ttu-id="d98e0-660">다음 예에서는 [Serilog](https://serilog.net/) 로거를 사용하여 `CreateWebHostBuilder`에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="d98e0-661">`AddSerilog`에서 `Log.Logger`에 지정된 정적 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d98e0-662">비동기 로거 메서드 미지원</span><span class="sxs-lookup"><span data-stu-id="d98e0-662">No asynchronous logger methods</span></span>

<span data-ttu-id="d98e0-663">로깅은 매우 빨라서 비동기 코드의 성능 비용을 들일 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d98e0-664">로깅 데이터 저장소가 느린 경우 직접 작성하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d98e0-665">로그 메시지를 처음에 빠른 저장소에 작성한 다음, 나중에 느린 저장소로 이동하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="d98e0-666">예를 들어 SQL Server에 로그하는 경우 `Log` 메서드는 동기식이므로 `Log` 메서드에서 직접 로그하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d98e0-667">대신 동기적으로 로그 메시지를 메모리 내 큐에 추가하고 백그라운드 작업자가 큐에서 메시지를 풀하여 SQL Server에 대해 비동기 데이터 푸시 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d98e0-668">자세한 내용은 [이](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 이슈를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="d98e0-669">Configuration</span><span class="sxs-lookup"><span data-stu-id="d98e0-669">Configuration</span></span>

<span data-ttu-id="d98e0-670">로깅 공급자 구성은 하나 이상의 구성 공급자에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="d98e0-671">파일 형식(INI, JSON 및 XML).</span><span class="sxs-lookup"><span data-stu-id="d98e0-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="d98e0-672">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="d98e0-672">Command-line arguments.</span></span>
* <span data-ttu-id="d98e0-673">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="d98e0-673">Environment variables.</span></span>
* <span data-ttu-id="d98e0-674">메모리 내 .NET 개체.</span><span class="sxs-lookup"><span data-stu-id="d98e0-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="d98e0-675">암호화되지 않은 [암호 관리자](xref:security/app-secrets) 저장소.</span><span class="sxs-lookup"><span data-stu-id="d98e0-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="d98e0-676">암호화된 사용자 저장소(예:[Azure Key Vault](xref:security/key-vault-configuration)).</span><span class="sxs-lookup"><span data-stu-id="d98e0-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="d98e0-677">사용자 지정 공급자(설치 또는 생성된).</span><span class="sxs-lookup"><span data-stu-id="d98e0-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="d98e0-678">예를 들어 로깅 구성은 일반적으로 앱 설정 파일의 `Logging` 섹션에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="d98e0-679">다음 예제에서는 일반적인 *appsettings.Development.json* 파일의 콘텐츠를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="d98e0-680">`Logging` 속성은 `LogLevel` 및 로그 공급자 속성을 포함할 수 있습니다(Console이 나타나 있습니다).</span><span class="sxs-lookup"><span data-stu-id="d98e0-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="d98e0-681">`Logging` 아래의 `LogLevel` 속성은 선택한 범주에 대해 로그할 최소 [수준](#log-level)을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d98e0-682">이 예제에서는 `System` 및 `Microsoft` 범주는 `Information` 수준으로 로그되고 다른 모든 범주는 `Debug` 수준으로 로그됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="d98e0-683">`Logging` 아래의 다른 속성은 로깅 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="d98e0-684">이 예제는 콘솔 공급자에 대한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-684">The example is for the Console provider.</span></span> <span data-ttu-id="d98e0-685">공급자가 [로그 범위](#log-scopes)를 지원하는 경우 `IncludeScopes`는 사용 가능 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d98e0-686">공급자 속성(예: 예제에서 `Console`)은 `LogLevel` 속성을 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="d98e0-687">공급자 아래의 `LogLevel`은 해당 공급자에 대한 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="d98e0-688">수준이 `Logging.{providername}.LogLevel`에 지정된 경우 `Logging.LogLevel`에 설정된 모든 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="d98e0-689">예를 들어, 다음과 같은 JSON을 가정해 봅시다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="d98e0-690">위의 JSON에서 `Console` 공급자 설정은 이전(기본) 로그 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="d98e0-691">로깅 API는 앱이 실행되는 동안 로그 수준을 변경하는 시나리오를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d98e0-692">그러나 일부 구성 공급자는 구성을 다시 로드할 수 있으며 이는 로깅 구성에 대한 즉각적인 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d98e0-693">예를 들어 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider)는 설정 파일을 읽기 위해 `CreateDefaultBuilder`에 의해 추가되며 기본적으로 로깅 구성을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="d98e0-694">앱이 실행되는 동안 코드에서 구성이 변경되면 앱 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*)를 호출하여 앱의 로깅 구성을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="d98e0-695">구성 공급자 구현에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="d98e0-696">샘플 로깅 출력</span><span class="sxs-lookup"><span data-stu-id="d98e0-696">Sample logging output</span></span>

<span data-ttu-id="d98e0-697">이전 섹션에 나와 있는 샘플 코드를 사용하면 명령줄에서 앱을 실행할 때 콘솔에 로그가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="d98e0-698">다음은 콘솔 출력의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="d98e0-699">위의 로그는 샘플 앱의 `http://localhost:5000/api/todo/0`에 HTTP Get 요청을 하여 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="d98e0-700">다음은 Visual Studio에서 샘플 앱을 실행할 때 디버그 창에 나타나는 것과 동일한 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="d98e0-701">이전 섹션에 표시된 `ILogger` 호출을 통해 생성된 로그는 "TodoApi"로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="d98e0-702">"Microsoft" 범주로 시작하는 로그는 ASP.NET Core 프레임워크 코드에서 온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d98e0-703">ASP.NET Core 및 애플리케이션 코드는 동일한 로깅 API와 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="d98e0-704">이 문서의 나머지 부분에서는 로깅에 대한 일부 세부 정보 및 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="d98e0-705">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="d98e0-705">NuGet packages</span></span>

<span data-ttu-id="d98e0-706">`ILogger` 및 `ILoggerFactory` 인터페이스는 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)에 있으며, 두 인터페이스의 기본 구현은 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="d98e0-707">로그 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-707">Log category</span></span>

<span data-ttu-id="d98e0-708">`ILogger` 개체가 생성되면 개체에 대한 *범주*가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="d98e0-709">해당 범주는 `ILogger`의 해당 인스턴스에서 만든 각 로그 메시지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d98e0-710">범주는 임의의 문자열일 수 있지만 "TodoApi.Controllers.TodoController"와 같은 클래스 이름을 사용하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="d98e0-711">`ILogger<T>`를 사용하여 `T`의 정규화된 형식 이름을 범주로 사용하는 `ILogger` 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="d98e0-712">범주를 명시적으로 지정하려면 `ILoggerFactory.CreateLogger`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="d98e0-713">`ILogger<T>`는 `T`의 정규화된 형식 이름으로 `CreateLogger`를 호출하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="d98e0-714">로그 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-714">Log level</span></span>

<span data-ttu-id="d98e0-715">모든 로그는 <xref:Microsoft.Extensions.Logging.LogLevel> 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="d98e0-716">로그 수준은 심각도 또는 중요도를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="d98e0-717">예를 들어 메서드가 정상적으로 종료되면 `Information` 로그를 작성하고 메서드가 *404 찾을 수 없음* 상태 코드를 반환하면 `Warning` 로그를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="d98e0-718">다음 코드는 `Information` 및 `Warning` 로그를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d98e0-719">위의 코드에서 `MyLogEvents.GetItem` 및 `MyLogEvents.GetItemNotFound` 매개 변수는 [로그 이벤트 ID](#log-event-id)입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="d98e0-720">두 번째 매개 변수는 나머지 메서드 매개 변수가 제공하는 인수 값에 대한 자리 표시자를 포함하고 있는 메시지 템플릿입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d98e0-721">메서드 매개 변수는 이 문서의 [로그 메시지 템플릿 섹션](#lmt)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="d98e0-722">메서드 이름에 수준을 포함하는 로그 메서드(예: `LogInformation` 및 `LogWarning`)는 [ILogger에 대한 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="d98e0-723">이 메서드들은 `LogLevel` 매개 변수를 사용하는 `Log` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="d98e0-724">이러한 확장 메서드 중 하나를 호출하는 대신 `Log` 메서드를 직접 호출할 수도 있지만, 구문이 비교적 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="d98e0-725">자세한 내용은 <xref:Microsoft.Extensions.Logging.ILogger> 및 [로거 확장 소스 코드](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="d98e0-726">ASP.NET Core는 다음과 같은 로그 수준을 정의하며, 여기에는 가장 낮은 심각도에서 가장 높은 심각도 순으로 정렬되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="d98e0-727">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="d98e0-727">Trace = 0</span></span>

  <span data-ttu-id="d98e0-728">일반적으로 디버깅에만 유용한 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="d98e0-729">이러한 메시지는 중요한 애플리케이션 데이터를 포함할 수 있으므로 프로덕션 환경에서 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="d98e0-730">*기본적으로 사용하지 않도록 설정됩니다.*</span><span class="sxs-lookup"><span data-stu-id="d98e0-730">*Disabled by default.*</span></span>

* <span data-ttu-id="d98e0-731">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="d98e0-731">Debug = 1</span></span>

  <span data-ttu-id="d98e0-732">개발 및 디버깅에 유용할 수 있는 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="d98e0-733">예: `Entering method Configure with flag set to true.` 로그 볼륨이 크기 때문에 문제 해결 시에만 `Debug` 수준 로그를 프로덕션 환경에서 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="d98e0-734">Information = 2</span><span class="sxs-lookup"><span data-stu-id="d98e0-734">Information = 2</span></span>

  <span data-ttu-id="d98e0-735">앱의 일반적인 흐름을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="d98e0-736">이러한 로그는 일반적으로 장기적인 가치가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="d98e0-737">예: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="d98e0-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="d98e0-738">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="d98e0-738">Warning = 3</span></span>

  <span data-ttu-id="d98e0-739">앱의 흐름에서 비정상적이거나 예기치 않은 이벤트를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="d98e0-740">앱이 중지되지는 않지만 조사해야 하는 오류 또는 기타 조건이 여기에 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="d98e0-741">처리된 예외는 `Warning` 로그 수준을 사용하는 일반적인 장소입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="d98e0-742">예: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="d98e0-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="d98e0-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="d98e0-743">Error = 4</span></span>

  <span data-ttu-id="d98e0-744">처리할 수 없는 오류 및 예외를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d98e0-745">이러한 메시지는 전체 앱 오류가 아닌 현재 동작 또는 작업(예: 현재 HTTP 요청)의 오류를 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="d98e0-746">예제 로그 메시지: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="d98e0-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="d98e0-747">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="d98e0-747">Critical = 5</span></span>

  <span data-ttu-id="d98e0-748">즉각적인 대응이 필요한 오류를 위한 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-748">For failures that require immediate attention.</span></span> <span data-ttu-id="d98e0-749">예: 데이터 손실 시나리오, 디스크 공간 부족.</span><span class="sxs-lookup"><span data-stu-id="d98e0-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="d98e0-750">로그 수준을 사용하여 특정 스토리지 매체 또는 디스플레이 창에 기록되는 로그 출력의 양을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="d98e0-751">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d98e0-751">For example:</span></span>

* <span data-ttu-id="d98e0-752">프로덕션:</span><span class="sxs-lookup"><span data-stu-id="d98e0-752">In production:</span></span>
  * <span data-ttu-id="d98e0-753">`Trace`~`Information` 수준 로깅은 자세한 로그 메시지를 대량으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d98e0-754">비용을 관리하고 데이터 저장소 제한을 초과하지 않으려면 `Trace`~`Information` 수준 메시지를 대용량, 저비용 데이터 저장소에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="d98e0-755">`Warning`~`Critical` 수준 로깅에서 생성되는 로그 메시지는 일반적으로 수량 및 용량이 더 적습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="d98e0-756">따라서 비용 및 스토리지 제한은 대개의 경우 문제가 되지 않으므로 데이터 스토리지를 유연하게 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="d98e0-757">개발 중:</span><span class="sxs-lookup"><span data-stu-id="d98e0-757">During development:</span></span>
  * <span data-ttu-id="d98e0-758">`Warning`~`Critical` 메시지를 콘솔에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="d98e0-759">문제 해결 시 `Trace`~`Information` 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="d98e0-760">이 문서의 뒷부분에 나오는 [로그 필터링](#log-filtering) 섹션에서는 공급자가 처리하는 로그 수준을 제어하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="d98e0-761">ASP.NET Core는 프레임워크 이벤트에 대한 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d98e0-762">이 문서 앞부분에 나온 로그 예제는 `Information` 수준 미만 로그를 제외했기 때문에 `Debug` 또는 `Trace` 수준 로그가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="d98e0-763">다음은 `Debug` 로그를 표시하도록 구성된 샘플 앱을 실행하여 생성된 콘솔 로그의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="d98e0-764">로그 이벤트 ID</span><span class="sxs-lookup"><span data-stu-id="d98e0-764">Log event ID</span></span>

<span data-ttu-id="d98e0-765">각 로그는 *이벤트 ID*를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d98e0-766">샘플 앱은 로컬에 정의된 `LoggingEvents` 클래스를 사용하여 이 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="d98e0-767">이벤트 ID는 이벤트 집합을 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-767">An event ID associates a set of events.</span></span> <span data-ttu-id="d98e0-768">예를 들어 페이지에서 항목 목록을 표시하는 것과 관련된 모든 로그는 1001일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d98e0-769">로깅 공급자는 이벤트 ID를 ID 필드, 로그 메시지에 저장하거나 또는 전혀 저장하지 않을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d98e0-770">디버그 공급자는 이벤트 ID를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d98e0-771">콘솔 공급자는 범주 뒤에 대괄호로 이벤트 ID를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="d98e0-772">로그 메시지 템플릿</span><span class="sxs-lookup"><span data-stu-id="d98e0-772">Log message template</span></span>

<span data-ttu-id="d98e0-773">각 로그는 메시지 템플릿을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-773">Each log specifies a message template.</span></span> <span data-ttu-id="d98e0-774">메시지 템플릿에는 인수가 제공되는 자리 표시자를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d98e0-775">번호가 아닌 자리 표시자의 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d98e0-776">값을 제공하는 데 사용되는 매개 변수를 결정하는 것은 자리 표시자의 이름이 아닌 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d98e0-777">다음 코드에서는 매개 변수 이름이 메시지 템플릿 순서와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d98e0-778">이 코드는 매개 변수 값을 순서대로 사용하여 로그 메시지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="d98e0-779">로깅 프레임워크는 로깅 공급자가 [구조화된 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있도록 이 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="d98e0-780">인수 자체는 서식이 지정된 메시지 템플릿뿐만 아니라 로깅 시스템에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d98e0-781">이 정보를 통해 로깅 공급자는 매개 변수 값을 필드로 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d98e0-782">예를 들어 로거 메서드 호출이 다음과 같다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d98e0-783">Azure Table Storage에 로그를 보내는 경우 각 Azure Table 엔터티는 로그 데이터에 대한 쿼리를 간소화하는 `ID` 및 `RequestTime` 속성을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="d98e0-784">쿼리는 문자 메시지의 시간 초과를 구문 분석하지 않고 특정 `RequestTime` 범위 내의 모든 로그를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="d98e0-785">예외 로깅</span><span class="sxs-lookup"><span data-stu-id="d98e0-785">Logging exceptions</span></span>

<span data-ttu-id="d98e0-786">로거 메서드는 다음 예제와 같이 예외를 전달할 수 있는 오버로드를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="d98e0-787">공급자마다 다양한 방식으로 예외 정보를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="d98e0-788">다음은 위에서 살펴본 코드의 디버그 공급자 출력의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="d98e0-789">로그 필터링</span><span class="sxs-lookup"><span data-stu-id="d98e0-789">Log filtering</span></span>

<span data-ttu-id="d98e0-790">특정 공급자 및 범주 또는 모든 공급자나 모든 범주의 최소 로그 수준을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="d98e0-791">최소 수준 미만인 로그는 해당 공급자에게 전달되지 않으므로 표시되거나 저장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="d98e0-792">모든 로그를 표시하지 않으려면 `LogLevel.None`을 최소 로그 수준으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="d98e0-793">`LogLevel.None`의 정수 값은 `LogLevel.Critical`(5)보다 높은 6입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="d98e0-794">구성에서 필터 규칙 만들기</span><span class="sxs-lookup"><span data-stu-id="d98e0-794">Create filter rules in configuration</span></span>

<span data-ttu-id="d98e0-795">프로젝트 템플릿 코드는 `CreateDefaultBuilder`를 호출하여 콘솔, 디버그 및 EventSource(ASP.NET Core 2.2 이상) 공급자에 대한 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="d98e0-796">[이 문서의 앞](#configuration)에서 설명한 것처럼 `CreateDefaultBuilder` 메서드는 `Logging` 섹션에서 구성을 조회하는 로깅을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="d98e0-797">구성 데이터는 다음 예제와 같이 공급자 및 범주별로 최소 로그 수준을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="d98e0-798">이 JSON은 6개의 필터 규칙을 만드는데, 하나는 디버그 공급자용이고, 4개는 콘솔 공급자용이고, 나머지 하나는 모든 공급자용입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="d98e0-799">`ILogger` 개체가 생성될 때 각 공급자에 대해 단일 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="d98e0-800">코드의 필터 규칙</span><span class="sxs-lookup"><span data-stu-id="d98e0-800">Filter rules in code</span></span>

<span data-ttu-id="d98e0-801">다음 예제는 코드에서 필터 규칙을 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="d98e0-802">두 번째 `AddFilter`는 형식 이름을 사용하여 디버그 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="d98e0-803">첫 번째 `AddFilter`는 공급자 형식을 지정하지 않으며, 따라서 모든 공급자에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d98e0-804">필터링 규칙 적용 방식</span><span class="sxs-lookup"><span data-stu-id="d98e0-804">How filtering rules are applied</span></span>

<span data-ttu-id="d98e0-805">이전 예제의 구성 데이터 및 `AddFilter` 코드는 다음 표에 표시된 규칙을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="d98e0-806">처음 6개는 구성 예제로부터 비롯된 것이고 마지막 2개는 코드 예제로부터 비롯된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="d98e0-807">번호</span><span class="sxs-lookup"><span data-stu-id="d98e0-807">Number</span></span> | <span data-ttu-id="d98e0-808">공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-808">Provider</span></span>      | <span data-ttu-id="d98e0-809">다음으로 시작하는 범주...</span><span class="sxs-lookup"><span data-stu-id="d98e0-809">Categories that begin with ...</span></span>          | <span data-ttu-id="d98e0-810">최소 로그 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="d98e0-811">1</span><span class="sxs-lookup"><span data-stu-id="d98e0-811">1</span></span>      | <span data-ttu-id="d98e0-812">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-812">Debug</span></span>         | <span data-ttu-id="d98e0-813">모든 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-813">All categories</span></span>                          | <span data-ttu-id="d98e0-814">정보</span><span class="sxs-lookup"><span data-stu-id="d98e0-814">Information</span></span>       |
| <span data-ttu-id="d98e0-815">2</span><span class="sxs-lookup"><span data-stu-id="d98e0-815">2</span></span>      | <span data-ttu-id="d98e0-816">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-816">Console</span></span>       | <span data-ttu-id="d98e0-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="d98e0-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="d98e0-818">Warning</span><span class="sxs-lookup"><span data-stu-id="d98e0-818">Warning</span></span>           |
| <span data-ttu-id="d98e0-819">3</span><span class="sxs-lookup"><span data-stu-id="d98e0-819">3</span></span>      | <span data-ttu-id="d98e0-820">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-820">Console</span></span>       | <span data-ttu-id="d98e0-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="d98e0-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="d98e0-822">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-822">Debug</span></span>             |
| <span data-ttu-id="d98e0-823">4</span><span class="sxs-lookup"><span data-stu-id="d98e0-823">4</span></span>      | <span data-ttu-id="d98e0-824">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-824">Console</span></span>       | <span data-ttu-id="d98e0-825">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="d98e0-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="d98e0-826">Error</span><span class="sxs-lookup"><span data-stu-id="d98e0-826">Error</span></span>             |
| <span data-ttu-id="d98e0-827">5</span><span class="sxs-lookup"><span data-stu-id="d98e0-827">5</span></span>      | <span data-ttu-id="d98e0-828">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-828">Console</span></span>       | <span data-ttu-id="d98e0-829">모든 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-829">All categories</span></span>                          | <span data-ttu-id="d98e0-830">정보</span><span class="sxs-lookup"><span data-stu-id="d98e0-830">Information</span></span>       |
| <span data-ttu-id="d98e0-831">6</span><span class="sxs-lookup"><span data-stu-id="d98e0-831">6</span></span>      | <span data-ttu-id="d98e0-832">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-832">All providers</span></span> | <span data-ttu-id="d98e0-833">모든 범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-833">All categories</span></span>                          | <span data-ttu-id="d98e0-834">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-834">Debug</span></span>             |
| <span data-ttu-id="d98e0-835">7</span><span class="sxs-lookup"><span data-stu-id="d98e0-835">7</span></span>      | <span data-ttu-id="d98e0-836">모든 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-836">All providers</span></span> | <span data-ttu-id="d98e0-837">시스템</span><span class="sxs-lookup"><span data-stu-id="d98e0-837">System</span></span>                                  | <span data-ttu-id="d98e0-838">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-838">Debug</span></span>             |
| <span data-ttu-id="d98e0-839">8</span><span class="sxs-lookup"><span data-stu-id="d98e0-839">8</span></span>      | <span data-ttu-id="d98e0-840">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-840">Debug</span></span>         | <span data-ttu-id="d98e0-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="d98e0-841">Microsoft</span></span>                               | <span data-ttu-id="d98e0-842">Trace</span><span class="sxs-lookup"><span data-stu-id="d98e0-842">Trace</span></span>             |

<span data-ttu-id="d98e0-843">`ILogger` 개체를 만들 때 `ILoggerFactory` 개체는 공급자마다 해당 로거에 적용할 단일 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d98e0-844">`ILogger` 인스턴스에서 작성된 모든 메시지는 선택한 규칙에 따라 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d98e0-845">사용 가능한 규칙 중에서 각 공급자 및 범주 쌍에 적용 가능한 가장 구체적인 규칙이 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d98e0-846">지정된 범주에 대한 `ILogger`가 생성되면 다음 알고리즘이 각 공급자에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d98e0-847">공급자 또는 공급자의 별칭과 일치하는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d98e0-848">일치하는 규칙이 없는 경우 빈 공급자가 있는 모든 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d98e0-849">앞 단계의 결과에서 일치하는 범주 접두사가 가장 긴 규칙을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d98e0-850">일치하는 규칙이 없는 경우 범주를 지정하지 않는 규칙을 모두 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d98e0-851">여러 규칙을 선택하는 경우 **마지막** 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d98e0-852">규칙을 선택하지 않는 경우 `MinimumLevel`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="d98e0-853">앞의 규칙 목록을 사용하여 “Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine” 범주에 대한 `ILogger` 개체를 만든다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="d98e0-854">디버그 공급자에게는 규칙 1, 6, 8이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="d98e0-855">규칙 8이 가장 구체적이므로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="d98e0-856">콘솔 공급자에게는 규칙 3, 4, 5, 6이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="d98e0-857">규칙 3이 가장 구체적입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="d98e0-858">결과 `ILogger` 인스턴스는 `Trace` 수준 이상의 로그를 디버그 공급자에게 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="d98e0-859">`Debug` 수준 이상의 로그는 콘솔 공급자에게 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="d98e0-860">공급자 별칭</span><span class="sxs-lookup"><span data-stu-id="d98e0-860">Provider aliases</span></span>

<span data-ttu-id="d98e0-861">각 공급자는 정규화된 형식 이름 대신 구성에서 사용할 수 있는 *별칭*을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="d98e0-862">기본 공급자의 경우 다음 별칭을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="d98e0-863">Console</span><span class="sxs-lookup"><span data-stu-id="d98e0-863">Console</span></span>
* <span data-ttu-id="d98e0-864">Debug</span><span class="sxs-lookup"><span data-stu-id="d98e0-864">Debug</span></span>
* <span data-ttu-id="d98e0-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-865">EventSource</span></span>
* <span data-ttu-id="d98e0-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="d98e0-866">EventLog</span></span>
* <span data-ttu-id="d98e0-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-867">TraceSource</span></span>
* <span data-ttu-id="d98e0-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d98e0-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="d98e0-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d98e0-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="d98e0-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d98e0-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="d98e0-871">기본 최소 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-871">Default minimum level</span></span>

<span data-ttu-id="d98e0-872">특정 공급자 및 범주에 대한 구성 또는 코드의 규칙이 적용되지 않는 경우에만 적용되는 최소 수준 설정이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="d98e0-873">다음 예제는 최소 수준을 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="d98e0-874">최소 수준을 명시적으로 설정하지 않으면 `Trace` 및 `Debug` 로그를 무시하는 `Information`이 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="d98e0-875">필터 함수</span><span class="sxs-lookup"><span data-stu-id="d98e0-875">Filter functions</span></span>

<span data-ttu-id="d98e0-876">필터 함수는 구성 또는 코드를 통해 규칙이 할당되지 않은 모든 공급자와 범주에 대해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="d98e0-877">함수의 코드는 공급자 형식, 범주 및 로그 수준에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="d98e0-878">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d98e0-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="d98e0-879">시스템 범주 및 수준</span><span class="sxs-lookup"><span data-stu-id="d98e0-879">System categories and levels</span></span>

<span data-ttu-id="d98e0-880">다음은 ASP.NET Core 및 Entity Framework Core에서 사용되는 몇 가지 범주로, 예상되는 로그에 대한 참고 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="d98e0-881">범주</span><span class="sxs-lookup"><span data-stu-id="d98e0-881">Category</span></span>                            | <span data-ttu-id="d98e0-882">참고</span><span class="sxs-lookup"><span data-stu-id="d98e0-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d98e0-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d98e0-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d98e0-884">일반 ASP.NET Core 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d98e0-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d98e0-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d98e0-886">고려되고, 발견되고, 사용된 키.</span><span class="sxs-lookup"><span data-stu-id="d98e0-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d98e0-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d98e0-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d98e0-888">허용되는 호스트.</span><span class="sxs-lookup"><span data-stu-id="d98e0-888">Hosts allowed.</span></span> |
| <span data-ttu-id="d98e0-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d98e0-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d98e0-890">HTTP 요청을 완료하는 데 걸린 시간과 시작 시간.</span><span class="sxs-lookup"><span data-stu-id="d98e0-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d98e0-891">로드된 호스팅 시작 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="d98e0-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d98e0-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d98e0-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d98e0-893">MVC 및 Razor 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d98e0-894">모델 바인딩, 필터 실행, 뷰 컴파일 작업 선택.</span><span class="sxs-lookup"><span data-stu-id="d98e0-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d98e0-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d98e0-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d98e0-896">경로 일치 정보.</span><span class="sxs-lookup"><span data-stu-id="d98e0-896">Route matching information.</span></span> |
| <span data-ttu-id="d98e0-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="d98e0-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d98e0-898">연결 시작, 중지 및 활성 응답 유지.</span><span class="sxs-lookup"><span data-stu-id="d98e0-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d98e0-899">HTTPS 인증서 정보.</span><span class="sxs-lookup"><span data-stu-id="d98e0-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d98e0-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d98e0-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d98e0-901">제공된 파일.</span><span class="sxs-lookup"><span data-stu-id="d98e0-901">Files served.</span></span> |
| <span data-ttu-id="d98e0-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d98e0-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d98e0-903">일반 Entity Framework Core 진단.</span><span class="sxs-lookup"><span data-stu-id="d98e0-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d98e0-904">데이터베이스 작업 및 구성, 변경 내용 검색, 마이그레이션.</span><span class="sxs-lookup"><span data-stu-id="d98e0-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="d98e0-905">로그 범위</span><span class="sxs-lookup"><span data-stu-id="d98e0-905">Log scopes</span></span>

 <span data-ttu-id="d98e0-906">*범위*는 논리적 작업 집합을 그룹화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d98e0-907">이 그룹화는 집합의 일부로 생성된 각 로그에 동일한 데이터를 연결하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d98e0-908">예를 들어 트랜잭션 처리의 일부로 생성되는 모든 로그에는 트랜잭션 ID가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d98e0-909">범위는 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 메서드에서 반환하는 `IDisposable` 형식이며 삭제될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="d98e0-910">`using` 블록에 로거 호출을 래핑하여 범위를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="d98e0-911">다음은 콘솔 공급자에 대한 범위를 사용하도록 설정하는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="d98e0-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d98e0-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="d98e0-913">범위 기반 로깅을 사용하려면 `IncludeScopes` 콘솔 로거 옵션을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="d98e0-914">구성에 대한 자세한 내용은 [구성](#configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="d98e0-915">각 로그 메시지는 범위 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="d98e0-916">기본 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-916">Built-in logging providers</span></span>

<span data-ttu-id="d98e0-917">ASP.NET Core는 다음 공급자를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="d98e0-918">콘솔</span><span class="sxs-lookup"><span data-stu-id="d98e0-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="d98e0-919">디버그</span><span class="sxs-lookup"><span data-stu-id="d98e0-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="d98e0-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="d98e0-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="d98e0-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="d98e0-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d98e0-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="d98e0-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d98e0-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d98e0-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d98e0-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d98e0-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d98e0-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="d98e0-926">ASP.NET Core 모듈을 사용한 표준 출력(stdout) 및 디버그 로깅에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis> 및 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="d98e0-927">콘솔 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-927">Console provider</span></span>

<span data-ttu-id="d98e0-928">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 공급자 패키지는 콘솔에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="d98e0-929">콘솔 로깅 출력을 보려면 프로젝트 폴더에서 명령 프롬프트를 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="d98e0-930">디버그 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-930">Debug provider</span></span>

<span data-ttu-id="d98e0-931">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 공급자 패키지는 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 클래스(`Debug.WriteLine` 메서드 호출)를 사용하여 로그 출력을 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="d98e0-932">Linux에서 이 공급자는 */var/log/message*에 로그를 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="d98e0-933">이벤트 원본 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-933">Event Source provider</span></span>

<span data-ttu-id="d98e0-934">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 공급자 패키지는 `Microsoft-Extensions-Logging` 이름을 사용하여 플랫폼 간 이벤트 원본에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d98e0-935">Windows에서 공급자는 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="d98e0-936">호스트를 빌드하기 위해 `CreateDefaultBuilder`가 호출되면 이벤트 원본 공급자가 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="d98e0-937">[PerfView 유틸리티](https://github.com/Microsoft/perfview)를 사용하여 로그를 수집하고 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d98e0-938">ETW 로그를 보는 다른 도구도 있지만, PerfView는 ASP.NET Core에서 내보내는 ETW 이벤트를 처리하기에 가장 좋은 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d98e0-939">이 공급자가 기록한 이벤트를 수집하도록 PerfView를 구성하려면 **추가 공급자** 목록에 `*Microsoft-Extensions-Logging` 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d98e0-940">(문자열의 시작 부분에 별표를 누락하지 마세요.)</span><span class="sxs-lookup"><span data-stu-id="d98e0-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview 추가 공급자](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="d98e0-942">Windows EventLog 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-942">Windows EventLog provider</span></span>

<span data-ttu-id="d98e0-943">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 공급자 패키지는 Windows 이벤트 로그에 로그 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="d98e0-944">[AddEventLog 오버로드](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)를 사용하여 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d98e0-945">`null`이거나 지정하지 않으면 다음 기본 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d98e0-946">`LogName`: “Application”</span><span class="sxs-lookup"><span data-stu-id="d98e0-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="d98e0-947">`SourceName`: “.NET Runtime”</span><span class="sxs-lookup"><span data-stu-id="d98e0-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="d98e0-948">`MachineName`: 로컬 머신 이름이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="d98e0-949">[경고 수준 이상](#log-level)의 이벤트가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="d98e0-950">다음 예제에서는 이벤트 로그 기본 로그 수준을 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="d98e0-951">TraceSource 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-951">TraceSource provider</span></span>

<span data-ttu-id="d98e0-952">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 공급자 패키지는 <xref:System.Diagnostics.TraceSource> 라이브러리 및 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="d98e0-953">[AddTraceSource 오버로드](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)를 사용하여 원본 스위치 및 추적 수신기를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="d98e0-954">이 공급자를 사용하려면 앱이 .NET Framework(.NET Core가 아닌)에서 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="d98e0-955">공급자는 샘플 앱에 사용된 <xref:System.Diagnostics.TextWriterTraceListener>와 같은 다양한 [수신기](/dotnet/framework/debug-trace-profile/trace-listeners)로 메시지를 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="d98e0-956">Azure App Service 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-956">Azure App Service provider</span></span>

<span data-ttu-id="d98e0-957">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자 패키지는 Azure App Service 앱의 파일 시스템과 Azure Storage 계정의 [Blob 스토리지](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)에 텍스트 파일을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="d98e0-958">이 공급자 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d98e0-959">.NET Framework를 대상으로 지정하거나 `Microsoft.AspNetCore.App` 메타패키지를 참조하는 경우 공급자 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="d98e0-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 오버로드로 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="d98e0-961">설정 개체는 로깅 출력 템플릿, BLOB 이름, 파일 크기 제한과 같은 기본 설정을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="d98e0-962">(*출력 템플릿*은 `ILogger` 메서드를 호출과 함께 제공되는 것 이외에 모든 로그에 적용되는 메시지 템플릿입니다.)</span><span class="sxs-lookup"><span data-stu-id="d98e0-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="d98e0-963">App Service 앱에 배포할 때 응용 프로그램은 Azure 포털 **App Service** 페이지의 [App Service 로그](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) 섹션에 있는 설정을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d98e0-964">다음 설정이 업데이트되면 앱을 다시 시작하거나 재배포하지 않아도 변경 내용이 즉시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d98e0-965">**애플리케이션 로깅(파일 시스템)**</span><span class="sxs-lookup"><span data-stu-id="d98e0-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d98e0-966">**애플리케이션 로깅(Blob)**</span><span class="sxs-lookup"><span data-stu-id="d98e0-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d98e0-967">로그 파일의 기본 위치는 *D:\\home\\LogFiles\\Application* 폴더이며, 기본 파일 이름은 *diagnostics-yyyymmdd.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d98e0-968">기본 파일 크기 제한은 10MB이고, 보존되는 기본 최대 파일 수는 2입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d98e0-969">기본 BLOB 이름은 *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d98e0-970">공급자는 프로젝트가 Azure 환경에서 실행되는 경우에만 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="d98e0-971">프로젝트를 로컬로 실행하는 경우에는 아무 영향도 없습니다&mdash;로컬 파일 또는 로컬 개발 스토리지에 Blob을 기록하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d98e0-972">Azure 로그 스트리밍</span><span class="sxs-lookup"><span data-stu-id="d98e0-972">Azure log streaming</span></span>

<span data-ttu-id="d98e0-973">Azure 로그 스트리밍을 통해 로그 작업을 실시간으로 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="d98e0-974">앱 서버</span><span class="sxs-lookup"><span data-stu-id="d98e0-974">The app server</span></span>
* <span data-ttu-id="d98e0-975">웹 서버</span><span class="sxs-lookup"><span data-stu-id="d98e0-975">The web server</span></span>
* <span data-ttu-id="d98e0-976">실패한 요청 추적</span><span class="sxs-lookup"><span data-stu-id="d98e0-976">Failed request tracing</span></span>

<span data-ttu-id="d98e0-977">Azure 로그 스트리밍을 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="d98e0-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d98e0-978">앱의 포털 페이지에서 **App Service 로그** 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="d98e0-979">**애플리케이션 로깅(파일 시스템)** 을 **On**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d98e0-980">로그 **수준**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-980">Choose the log **Level**.</span></span> <span data-ttu-id="d98e0-981">이 설정은 앱의 다른 로깅 공급자가 아닌 Azure 로그 스트리밍에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="d98e0-982">**로그 스트림** 페이지로 이동하여 앱 메시지를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="d98e0-983">앱이 `ILogger` 인터페이스를 통해 기록한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="d98e0-984">Azure Application Insights 추적 로깅</span><span class="sxs-lookup"><span data-stu-id="d98e0-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="d98e0-985">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 공급자 패키지는 Azure Application Insights에 로그를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="d98e0-986">Application Insights는 웹앱을 모니터링하고 원격 분석 데이터를 쿼리 및 분석하기 위한 도구를 제공하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d98e0-987">이 공급자를 사용하는 경우 Application Insights 도구를 사용하여 로그를 쿼리 및 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d98e0-988">로깅 공급자는 ASP.NET Core에 대한 모든 사용 가능한 원격 분석을 제공하는 패키지인 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)의 종속성으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d98e0-989">이 패키지를 사용하는 경우 공급자 패키지를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d98e0-990">ASP.NET 4.x용으로 제공되는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 패키지&mdash;를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="d98e0-991">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d98e0-992">Application Insights 개요</span><span class="sxs-lookup"><span data-stu-id="d98e0-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d98e0-993">[ASP.NET Core 애플리케이션용 Application Insights](/azure/azure-monitor/app/asp-net-core) - 로깅과 함께 전체 범위 Application Insights 원격 분석을 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d98e0-994">[.NET Core ILogger 로그용 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 나머지 Application Insights 원격 분석 없이 로깅 공급자를 구현하려면 여기에서 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d98e0-995">[Application Insights 로깅 어댑터](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="d98e0-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d98e0-996">[Application Insights SDK 설치, 구성 및 초기화](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 사이트의 대화형 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d98e0-997">타사 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="d98e0-997">Third-party logging providers</span></span>

<span data-ttu-id="d98e0-998">ASP.NET Core와 호환되는 타사 로깅 프레임워크는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d98e0-999">[elmah.io](https://elmah.io/)([GitHub 리포지토리](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)([GitHub 리포지토리](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d98e0-1001">[JSNLog](https://jsnlog.com/)([GitHub 리포지토리](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d98e0-1002">[KissLog.net](https://kisslog.net/)([GitHub 리포지토리](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d98e0-1003">[Log4Net](https://logging.apache.org/log4net/)([GitHub 리포지토리](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d98e0-1004">[Loggr](https://loggr.net/)([GitHub 리포지토리](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-1005">[NLog](https://nlog-project.org/)([GitHub 리포지토리](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d98e0-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d98e0-1007">[Serilog](https://serilog.net/)([GitHub 리포지토리](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d98e0-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)([Github 리포지토리](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d98e0-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d98e0-1009">일부 타사 프레임워크는 [구조적 로깅이라고 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d98e0-1010">타사 프레임워크를 사용하는 방법은 다음과 같이 기본 공급자 중 하나를 사용하는 방법과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d98e0-1011">프로젝트에 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d98e0-1012">로깅 프레임워크에서 제공하는 `ILoggerFactory` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d98e0-1013">자세한 내용은 각 공급자의 설명서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d98e0-1014">타사 로깅 공급자는 Microsoft에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d98e0-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d98e0-1015">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d98e0-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
