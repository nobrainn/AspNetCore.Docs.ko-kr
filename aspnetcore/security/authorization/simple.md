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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="c6776-103">ASP.NET Core의 단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="c6776-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="c6776-104">MVC의 권한 부여는 `AuthorizeAttribute` 특성과 해당 하는 다양 한 매개 변수를 통해 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="c6776-105">가장 간단한 방법으로, 컨트롤러 `AuthorizeAttribute` 또는 작업에 특성을 적용 하면 인증 된 사용자에 대 한 컨트롤러나 동작에 대 한 액세스가 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="c6776-106">예를 들어 다음 코드는에 대 한 액세스 `AccountController` 를 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="c6776-107">컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 작업 자체에 `AuthorizeAttribute` 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="c6776-108">이제 인증 된 사용자만 함수에 `Logout` 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="c6776-109">또한 `AllowAnonymous` 특성을 사용 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="c6776-110">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="c6776-110">For example:</span></span>

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

<span data-ttu-id="c6776-111">이렇게 하면 인증 된 사용자 또는 인증 되지 `AccountController`않은/익명 상태 `Login` 에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="c6776-112">`[AllowAnonymous]`모든 권한 부여 문을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="c6776-113">및 `[AllowAnonymous]` `[Authorize]` 특성을 함께 사용할 경우 `[Authorize]` 특성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="c6776-114">예를 들어 컨트롤러 수준 `[AllowAnonymous]` 에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c6776-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
