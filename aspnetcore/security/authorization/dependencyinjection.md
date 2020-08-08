---
title: ASP.NET Core 요구 사항 처리기의 종속성 주입
author: rick-anderson
description: 종속성 주입을 사용 하 여 ASP.NET Core 앱에 권한 부여 요구 사항 처리기를 삽입 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021005"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core 요구 사항 처리기의 종속성 주입

<a name="security-authorization-di"></a>

[권한 부여 처리기](xref:security/authorization/policies#handler-registration) 는 구성 중에 ( [종속성 주입](xref:fundamentals/dependency-injection)사용) 서비스 컬렉션에 등록 되어야 합니다.

권한 부여 처리기 내에서 평가 하려는 규칙의 리포지토리가 있고 해당 리포지토리가 서비스 컬렉션에 등록 되어 있다고 가정 합니다. 권한 부여는이를 확인 하 고 생성자에 삽입 합니다.

예를 들어 ASP를 사용 하려는 경우입니다. 처리기에 삽입 하려는 NET의 로깅 인프라 `ILoggerFactory` 입니다. 이러한 처리기는 다음과 같을 수 있습니다.

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

다음을 사용 하 여 처리기를 등록 합니다 `services.AddSingleton()` .

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

처리기의 인스턴스는 응용 프로그램이 시작 될 때 만들어지며, DI는 등록 된을 `ILoggerFactory` 생성자에 삽입 합니다.

> [!NOTE]
> Entity Framework를 사용 하는 처리기는 단일 항목로 등록 하면 안 됩니다.
