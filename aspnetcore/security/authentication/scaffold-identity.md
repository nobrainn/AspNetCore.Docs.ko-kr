---
title: ASP.NET Core Identity 프로젝트의 스 캐 폴드
author: rick-anderson
description: ASP.NET Core 프로젝트에서 스 캐 폴드 Identity 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768392"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="a7004-103">ASP.NET Core Identity 프로젝트의 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="a7004-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7004-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a7004-105">ASP.NET Core [은 Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core Identity ](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a7004-106">를 포함 Identity 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a7004-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a7004-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a7004-109">생성 된 코드는 Identity rcl의 동일한 코드 보다 우선적으로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a7004-110">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a7004-111">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl Identity 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a7004-112">생성할 코드를 선택할 Identity 수 있는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a7004-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="a7004-114">이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a7004-115">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a7004-116">스 캐 폴더를 Identity 실행 한 후에 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="a7004-117">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 Identity경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a7004-118">스 캐 폴딩 Identity할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a7004-119">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a7004-120">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="a7004-121">기존 개별 Identity 계정이 있는 프로젝트에 새 데이터 컨텍스트를 사용 하 여 스 캐 폴딩 할 때:</span><span class="sxs-lookup"><span data-stu-id="a7004-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="a7004-122">에서 `Startup.ConfigureServices`에 대 한 호출을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="a7004-123">예를 들어 `AddDbContext` 및 `AddDefaultIdentity` 는 다음 코드에서 주석 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="a7004-124">앞 코드는 *영역/Identity/IdentityHostingStartup.cs* 에 중복 된 코드를 주석으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="a7004-125">일반적으로 개별 계정을 사용 하 여 만든 앱은 새 데이터 컨텍스트 ***를 만들지 않아야 합니다.***</span><span class="sxs-lookup"><span data-stu-id="a7004-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a7004-126">빈 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a7004-127">다음과 유사한 `Startup` 코드를 사용 하 여 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a7004-128">기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-128">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="a7004-129">는 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-130">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a7004-131">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="a7004-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a7004-132">인증 사용</span><span class="sxs-lookup"><span data-stu-id="a7004-132">Enable authentication</span></span>

<span data-ttu-id="a7004-133">다음과 유사한 `Startup` 코드를 사용 하 여 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a7004-134">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="a7004-134">Layout changes</span></span>

<span data-ttu-id="a7004-135">선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a7004-136">권한 부여를 사용 Razor 하 여 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-136">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="a7004-137">일부 Identity 옵션은 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-138">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a7004-139">기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-139">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a7004-140">선택 사항:`_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="a7004-141">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="a7004-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="a7004-142">는 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-143">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a7004-144">다음과 유사한 `Startup` 코드를 사용 하 여 클래스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a7004-145">권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a7004-146">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="a7004-146">Create full identity UI source</span></span>

<span data-ttu-id="a7004-147">Identity UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a7004-148">다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱 Identity Identity 에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a7004-149">이 작업을 수행 하 여 Identity UI에 대 한 모든 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a7004-150">기본값 Identity 은 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a7004-151">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a7004-152">`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a7004-153">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="a7004-153">Disable register page</span></span>

<span data-ttu-id="a7004-154">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="a7004-154">To disable user registration:</span></span>

* <span data-ttu-id="a7004-155">스 캐 폴드 Identity.</span><span class="sxs-lookup"><span data-stu-id="a7004-155">Scaffold Identity.</span></span> <span data-ttu-id="a7004-156">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a7004-157">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a7004-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a7004-158">사용자가이 끝점에서 등록할 수 없도록 *영역/Identity/Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a7004-159">이전 변경 내용과 일치 하도록 *영역/Identity/Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a7004-160">*영역/Identity/Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a7004-161">*영역/Identity/Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a7004-162">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a7004-163">에서 확인 코드를 제거 합니다 `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="a7004-163">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a7004-164">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="a7004-164">Use another app to add users</span></span>

<span data-ttu-id="a7004-165">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a7004-166">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-166">Options to add users include:</span></span>

* <span data-ttu-id="a7004-167">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="a7004-168">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-168">A console app.</span></span>

<span data-ttu-id="a7004-169">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a7004-170">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="a7004-171">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a7004-172">사용자가 Identity 데이터베이스에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a7004-173">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a7004-174">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a7004-175">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a7004-176">정적 Identity 자산 게시 방지</span><span class="sxs-lookup"><span data-stu-id="a7004-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a7004-177">정적 Identity 자산을 웹 루트에 게시 하지 않으려면를 참조 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>하십시오.</span><span class="sxs-lookup"><span data-stu-id="a7004-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7004-178">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a7004-178">Additional resources</span></span>

* [<span data-ttu-id="a7004-179">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="a7004-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7004-180">ASP.NET Core 2.1 이상에서는 [ Identity ASP.NET Core](xref:security/authentication/identity) 를 [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a7004-181">를 포함 Identity 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a7004-182">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a7004-183">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a7004-184">생성 된 코드는 Identity rcl의 동일한 코드 보다 우선적으로 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a7004-185">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a7004-186">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl Identity 패키지를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a7004-187">생성할 코드를 선택할 Identity 수 있는 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a7004-188">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="a7004-189">이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a7004-190">Identity 스 캐 폴더를 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme* 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="a7004-191">*ScaffoldingReadme* 파일은 스 캐 폴딩 업데이트를 완료 하는 Identity 데 필요한 사항에 대 한 일반적인 지침을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="a7004-192">이 문서에는 *ScaffoldingReadme* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="a7004-193">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a7004-194">스 캐 폴더를 Identity 실행 한 후에 변경 내용을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="a7004-195">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 Identity경우에 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a7004-196">스 캐 폴딩 Identity할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a7004-197">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a7004-198">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a7004-199">빈 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a7004-200">`Startup` 클래스에 다음의 강조 표시 된 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a7004-201">기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-201">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="a7004-202">는 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-203">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a7004-204">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="a7004-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a7004-205">인증 사용</span><span class="sxs-lookup"><span data-stu-id="a7004-205">Enable authentication</span></span>

<span data-ttu-id="a7004-206">클래스의 `Configure` 메서드에서 다음 `UseStaticFiles`이후에 [useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다. `Startup`</span><span class="sxs-lookup"><span data-stu-id="a7004-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a7004-207">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="a7004-207">Layout changes</span></span>

<span data-ttu-id="a7004-208">선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a7004-209">권한 부여를 사용 Razor 하 여 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-209">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="a7004-210">일부 Identity 옵션은 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-211">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a7004-212">기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-212">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a7004-213">선택 사항:`_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="a7004-214">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="a7004-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="a7004-215">는 *영역/Identity/IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a7004-216">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="a7004-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a7004-217">다음 이후에 `UseStaticFiles` [useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 호출:</span><span class="sxs-lookup"><span data-stu-id="a7004-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a7004-218">권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="a7004-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="a7004-219">*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a7004-220">전체 id UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="a7004-220">Create full identity UI source</span></span>

<span data-ttu-id="a7004-221">Identity UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a7004-222">다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱 Identity Identity 에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a7004-223">이 작업을 수행 하 여 Identity UI에 대 한 모든 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a7004-224">기본값 Identity 은 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a7004-225">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a7004-226">`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a7004-227">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="a7004-227">Disable register page</span></span>

<span data-ttu-id="a7004-228">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="a7004-228">To disable user registration:</span></span>

* <span data-ttu-id="a7004-229">스 캐 폴드 Identity.</span><span class="sxs-lookup"><span data-stu-id="a7004-229">Scaffold Identity.</span></span> <span data-ttu-id="a7004-230">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a7004-231">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="a7004-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a7004-232">사용자가이 끝점에서 등록할 수 없도록 *영역/Identity/Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a7004-233">이전 변경 내용과 일치 하도록 *영역/Identity/Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a7004-234">*영역/Identity/Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a7004-235">*영역/Identity/Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a7004-236">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a7004-237">에서 확인 코드를 제거 합니다 `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="a7004-237">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a7004-238">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="a7004-238">Use another app to add users</span></span>

<span data-ttu-id="a7004-239">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a7004-240">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-240">Options to add users include:</span></span>

* <span data-ttu-id="a7004-241">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="a7004-242">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-242">A console app.</span></span>

<span data-ttu-id="a7004-243">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a7004-244">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="a7004-245">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a7004-246">사용자가 Identity 데이터베이스에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a7004-247">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a7004-248">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a7004-249">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a7004-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7004-250">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a7004-250">Additional resources</span></span>

* [<span data-ttu-id="a7004-251">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="a7004-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end