---
title: ASP.NET Core 요구 사항 처리기의 종속성 주입
author: rick-anderson
description: 종속성 주입을 사용 하 여 ASP.NET Core 앱에 권한 부여 요구 사항 처리기를 삽입 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775122"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="61787-103">ASP.NET Core 요구 사항 처리기의 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="61787-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="61787-104">[권한 부여 처리기](xref:security/authorization/policies#handler-registration) 는 구성 중에 ( [종속성 주입](xref:fundamentals/dependency-injection)사용) 서비스 컬렉션에 등록 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61787-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="61787-105">권한 부여 처리기 내에서 평가 하려는 규칙의 리포지토리가 있고 해당 리포지토리가 서비스 컬렉션에 등록 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="61787-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="61787-106">권한 부여는이를 확인 하 고 생성자에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="61787-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="61787-107">예를 들어 ASP를 사용 하려는 경우입니다. 처리기에 삽입 `ILoggerFactory` 하려는 NET의 로깅 인프라입니다.</span><span class="sxs-lookup"><span data-stu-id="61787-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="61787-108">이러한 처리기는 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61787-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="61787-109">다음을 사용 `services.AddSingleton()`하 여 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="61787-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="61787-110">처리기의 인스턴스는 응용 프로그램이 시작 될 때 만들어지며, DI는 등록 `ILoggerFactory` 된을 생성자에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="61787-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="61787-111">Entity Framework를 사용 하는 처리기는 단일 항목로 등록 하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="61787-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
