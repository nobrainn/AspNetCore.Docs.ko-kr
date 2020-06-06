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
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272127"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="796fe-103">ASP.NET Core의 단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="796fe-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="796fe-104">ASP.NET Core의 권한 부여는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 및 해당 하는 다양 한 매개 변수를 사용 하 여 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="796fe-105">가장 간단한 형태의 `[Authorize]` 특성을 컨트롤러, 작업 또는 페이지에 적용 하면 Razor 해당 구성 요소에 대 한 액세스를 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="796fe-106">예를 들어 다음 코드는에 대 한 액세스를 `AccountController` 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="796fe-107">컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 `AuthorizeAttribute` 작업 자체에 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="796fe-108">이제 인증 된 사용자만 함수에 액세스할 수 있습니다 `Logout` .</span><span class="sxs-lookup"><span data-stu-id="796fe-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="796fe-109">또한 특성을 사용 `AllowAnonymous` 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="796fe-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-110">For example:</span></span>

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

<span data-ttu-id="796fe-111">이렇게 하면 인증 된 사용자 `AccountController` `Login` 또는 인증 되지 않은/익명 상태에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="796fe-112">`[AllowAnonymous]`모든 권한 부여 문을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="796fe-113">`[AllowAnonymous]`및 특성을 함께 사용할 경우 `[Authorize]` `[Authorize]` 특성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="796fe-114">예를 들어 `[AllowAnonymous]` 컨트롤러 수준에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="796fe-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
