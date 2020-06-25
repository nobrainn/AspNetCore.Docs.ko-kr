---
title: ASP.NET Core Blazor 로깅
author: guardrex
description: 로그 수준 구성, Razor 구성 요소로부터 로그 메시지를 작성하는 방법을 포함하여 Blazor 앱의 로깅에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103326"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="8e48c-103">ASP.NET Core Blazor 로깅</span><span class="sxs-lookup"><span data-stu-id="8e48c-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="8e48c-104"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8e48c-104"> WebAssembly</span></span>

<span data-ttu-id="8e48c-105">`Program.Main`의 `WebAssemblyHostBuilder.Logging` 속성을 사용하여 Blazor WebAssembly 앱에서 로깅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="8e48c-106">`Logging` 속성은 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>형식이므로 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>에서 사용할 수 있는 모든 확장 메서드를 `Logging`에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="8e48c-107">로깅 구성은 앱 설정 파일에서 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="8e48c-108">자세한 내용은 <xref:blazor/fundamentals/logging>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8e48c-108">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="8e48c-109"> 서버</span><span class="sxs-lookup"><span data-stu-id="8e48c-109"> Server</span></span>

<span data-ttu-id="8e48c-110">일반적인 ASP.NET Core 로깅 지침은 <xref:fundamentals/logging/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8e48c-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="8e48c-111"> WebAssembly SignalR .NET 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="8e48c-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="8e48c-112"><xref:Microsoft.Extensions.Logging.ILoggerProvider>를 삽입하여 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>로 전달된 로깅 공급자에 `WebAssemblyConsoleLogger`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="8e48c-113">기존의 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>와 달리 `WebAssemblyConsoleLogger`는 브라우저별 로깅 API 주위의 래퍼입니다(예: `console.log`).</span><span class="sxs-lookup"><span data-stu-id="8e48c-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="8e48c-114">`WebAssemblyConsoleLogger`를 사용하면 브라우저 컨텍스트의 Mono 내에서 로깅을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="8e48c-115">Razor 구성 요소의 로그</span><span class="sxs-lookup"><span data-stu-id="8e48c-115">Log in Razor components</span></span>

<span data-ttu-id="8e48c-116">로거는 앱 시작 구성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="8e48c-117"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>와 같은 API의 Intellisense 작성을 지원하려면 <xref:Microsoft.Extensions.Logging>의 `using` 지시문이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="8e48c-118">다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILogger>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="8e48c-119">다음 예제에서는 Razor 구성 요소에서 <xref:Microsoft.Extensions.Logging.ILoggerFactory>를 사용하여 로깅하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8e48c-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="8e48c-120">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8e48c-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
