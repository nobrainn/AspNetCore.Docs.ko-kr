---
title: ASP.NET Core의 단순 권한 부여
author: rick-anderson
description: 권한 부여 특성을 사용 하 여 ASP.NET Core 컨트롤러 및 작업에 대 한 액세스를 제한 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775637"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core의 단순 권한 부여

<a name="security-authorization-simple"></a>

MVC의 권한 부여는 `AuthorizeAttribute` 특성과 해당 하는 다양 한 매개 변수를 통해 제어 됩니다. 가장 간단한 방법으로, 컨트롤러 `AuthorizeAttribute` 또는 작업에 특성을 적용 하면 인증 된 사용자에 대 한 컨트롤러나 동작에 대 한 액세스가 제한 됩니다.

예를 들어 다음 코드는에 대 한 액세스 `AccountController` 를 인증 된 사용자로 제한 합니다.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 작업 자체에 `AuthorizeAttribute` 특성을 적용 합니다.

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

이제 인증 된 사용자만 함수에 `Logout` 액세스할 수 있습니다.

또한 `AllowAnonymous` 특성을 사용 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다. 예를 들어:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

이렇게 하면 인증 된 사용자 또는 인증 되지 `AccountController`않은/익명 상태 `Login` 에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.

> [!WARNING]
> `[AllowAnonymous]`모든 권한 부여 문을 무시 합니다. 및 `[AllowAnonymous]` `[Authorize]` 특성을 함께 사용할 경우 `[Authorize]` 특성이 무시 됩니다. 예를 들어 컨트롤러 수준 `[AllowAnonymous]` 에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.
