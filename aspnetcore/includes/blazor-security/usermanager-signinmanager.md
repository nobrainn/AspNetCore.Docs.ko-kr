## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="ac654-101">사용자 관리자 및 사인 관리자</span><span class="sxs-lookup"><span data-stu-id="ac654-101">UserManager and SignInManager</span></span>

<span data-ttu-id="ac654-102">서버 앱에 다음이 필요한 경우 사용자 식별자 클레임 유형을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac654-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="ac654-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601>또는 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> API 끝점에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac654-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="ac654-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>사용자 이름, 이메일 주소 또는 잠금 종료 시간과 같은 세부 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="ac654-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="ac654-105">`Startup.ConfigureServices`의 경우:</span><span class="sxs-lookup"><span data-stu-id="ac654-105">In `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="ac654-106">다음은 `WeatherForecastController` 메서드가 <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> 호출될 `Get` 때를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="ac654-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using BlazorAppIdentityServer.Server.Models;
using BlazorAppIdentityServer.Shared;

namespace BlazorAppIdentityServer.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```