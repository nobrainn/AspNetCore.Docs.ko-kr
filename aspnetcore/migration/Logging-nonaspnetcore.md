---
title: Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션
author: pakrym
description: 2.1에서 2.2 또는 3.0로 non-ASP.NET를 사용 하는 Core 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777061"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="ab6d8-103">Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="ab6d8-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="ab6d8-104">이 문서에서는 2.1에서 2.2 또는 3.0로를 사용 `Microsoft.Extensions.Logging` 하는 non-ASP.NET Core 응용 프로그램을 마이그레이션하는 일반적인 단계를 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab6d8-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="ab6d8-105">2.1-2.2</span><span class="sxs-lookup"><span data-stu-id="ab6d8-105">2.1 to 2.2</span></span>

<span data-ttu-id="ab6d8-106">수동으로를 `ServiceCollection` 만들고 호출 `AddLogging`합니다.</span><span class="sxs-lookup"><span data-stu-id="ab6d8-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="ab6d8-107">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="ab6d8-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ab6d8-108">2.2 예제:</span><span class="sxs-lookup"><span data-stu-id="ab6d8-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="ab6d8-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="ab6d8-109">2.1 to 3.0</span></span>

<span data-ttu-id="ab6d8-110">3.0에서는을 사용 `LoggingFactory.Create`합니다.</span><span class="sxs-lookup"><span data-stu-id="ab6d8-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="ab6d8-111">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="ab6d8-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ab6d8-112">3.0 예제:</span><span class="sxs-lookup"><span data-stu-id="ab6d8-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="ab6d8-113">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ab6d8-113">Additional resources</span></span>

<xref:fundamentals/logging/index>