---
제목: ASP.NET Core 작성자의 간단한 권한 부여: rick-anderson description: 권한 부여 특성을 사용 하 여 ASP.NET Core 컨트롤러 및 작업에 대 한 액세스를 제한 하는 방법을 알아봅니다.
ms author: riande ms. date: 10/14/2016 no loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 보안/권한 부여/단순

---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core의 단순 권한 부여

<a name="security-authorization-simple"></a>

ASP.NET Core의 권한 부여는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 및 해당 하는 다양 한 매개 변수를 사용 하 여 제어 됩니다. 가장 간단한 형태의 `[Authorize]` 특성을 컨트롤러, 작업 또는 페이지에 적용 하면 Razor 해당 구성 요소에 대 한 액세스를 인증 된 사용자로 제한 합니다.

예를 들어 다음 코드는에 대 한 액세스를 `AccountController` 인증 된 사용자로 제한 합니다.

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

컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 `AuthorizeAttribute` 작업 자체에 특성을 적용 합니다.

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

이제 인증 된 사용자만 함수에 액세스할 수 있습니다 `Logout` .

또한 특성을 사용 `AllowAnonymous` 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다. 다음은 그 예입니다.

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

이렇게 하면 인증 된 사용자 `AccountController` `Login` 또는 인증 되지 않은/익명 상태에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.

> [!WARNING]
> `[AllowAnonymous]`모든 권한 부여 문을 무시 합니다. `[AllowAnonymous]`및 특성을 함께 사용할 경우 `[Authorize]` `[Authorize]` 특성이 무시 됩니다. 예를 들어 `[AllowAnonymous]` 컨트롤러 수준에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.
