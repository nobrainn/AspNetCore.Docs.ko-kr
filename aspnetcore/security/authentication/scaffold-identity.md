---
title: IdentityASP.NET Core 프로젝트의 스 캐 폴드
author: rick-anderson
description: ASP.NET Core 프로젝트에서 스 캐 폴드 하는 방법에 대해 알아봅니다 Identity .
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
ms.openlocfilehash: 116e5d27e7585e9168db433480c3a5e9d08379f3
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454678"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="58994-103">IdentityASP.NET Core 프로젝트의 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="58994-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58994-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58994-105">ASP.NET Core은 [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core Identity ](xref:security/authentication/identity) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="58994-106">를 포함 하는 응용 프로그램 Identity 은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="58994-107">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="58994-108">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="58994-109">생성된 코드는 Identity RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="58994-110">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 Identity Ui 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="58994-111">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="58994-112">선택한 Identity 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="58994-113">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="58994-114">이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="58994-115">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="58994-116">스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="58994-117">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="58994-118">스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="58994-119">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="58994-120">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="58994-121">Identity기존 개별 계정이 있는 프로젝트에 새 데이터 컨텍스트를 사용 하 여 스 캐 폴딩 할 때:</span><span class="sxs-lookup"><span data-stu-id="58994-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="58994-122">에서 `Startup.ConfigureServices` 에 대 한 호출을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="58994-123">예를 들어 `AddDbContext` 및 `AddDefaultIdentity` 는 다음 코드에서 주석 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="58994-124">앞 코드는 *영역/ Identity /IdentityHostingStartup.cs* 에 중복 된 코드를 주석으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="58994-125">일반적으로 개별 계정을 사용 하 여 만든 앱은 새 데이터 컨텍스트 ***를 만들지 않아야 합니다.***</span><span class="sxs-lookup"><span data-stu-id="58994-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="58994-126">Identity빈 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="58994-127">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="58994-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="58994-128">Identity Razor 기존 권한 부여 없이 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="58994-129">는 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-130">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="58994-131">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="58994-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="58994-132">인증 사용</span><span class="sxs-lookup"><span data-stu-id="58994-132">Enable authentication</span></span>

<span data-ttu-id="58994-133">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="58994-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="58994-134">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="58994-134">Layout changes</span></span>

<span data-ttu-id="58994-135">선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="58994-136">Identity Razor 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="58994-137">일부 Identity 옵션은 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-138">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="58994-139">Identity기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="58994-140">선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="58994-141">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="58994-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="58994-142">는 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-143">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="58994-144">다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="58994-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="58994-145">Identity권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="58994-146">Identity Blazor 기존 권한 부여를 사용 하지 않고 서버 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="58994-147">는 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-148">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="58994-149">마이그레이션</span><span class="sxs-lookup"><span data-stu-id="58994-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="58994-150">앱에 XSRF 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="58994-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="58994-151">토큰은 구성 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="58994-152">인증 토큰을 프로 비전 하 여 인증 쿠키에 저장 하는 경우 구성 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="58994-153">구성 요소 `HttpContext` 는 직접 사용할 수 없으므로의 로그 아웃 끝점에 게시 하기 위해 [XSRF (요청 방지 위조) 토큰](xref:security/anti-request-forgery) 을 얻을 수 있는 방법이 없습니다 Identity `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="58994-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="58994-154">XSRF 토큰을 구성 요소에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="58994-155">자세한 내용은 <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-155">For more information, see <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="58994-156">*Pages/_Host* 파일에서 및 클래스에 토큰을 추가한 후에 토큰을 설정 합니다 `InitialApplicationState` `TokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="58994-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="58994-157">`App`를 할당 하기 위해 구성 요소 (*응용 프로그램 razor*)를 업데이트 합니다 `InitialState.XsrfToken` .</span><span class="sxs-lookup"><span data-stu-id="58994-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="58994-158">항목에서 설명 하는 `TokenProvider` 서비스는 `LoginDisplay` 다음 [레이아웃 및 인증 흐름 변경](#layout-and-authentication-flow-changes) 섹션의 구성 요소에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="58994-159">인증 사용</span><span class="sxs-lookup"><span data-stu-id="58994-159">Enable authentication</span></span>

<span data-ttu-id="58994-160">클래스에서 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="58994-160">In the `Startup` class:</span></span>

* <span data-ttu-id="58994-161">Razor에서 Pages services가 추가 되었는지 확인 `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="58994-162">[Tokenprovider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app)를 사용 하는 경우 서비스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-162">If using the [TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="58994-163">`UseDatabaseErrorPage` `Startup.Configure` 개발 환경에 대 한의 응용 프로그램 작성기에서를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="58994-164">`UseAuthentication`및 이후를 호출 `UseAuthorization` `UseRouting` 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="58994-165">페이지에 대 한 끝점을 추가 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="58994-166">레이아웃 및 인증 흐름 변경</span><span class="sxs-lookup"><span data-stu-id="58994-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="58994-167">`RedirectToLogin`프로젝트 루트의 앱 *공유* 폴더에 구성 요소 (*redirecttologin. razor*)를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`앱의 *공유* 폴더에 구성 요소 (*LoginDisplay*)를 추가 합니다. <span data-ttu-id="58994-169">[Tokenprovider 서비스](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app) 는 Identity 의 로그 아웃 끝점에 게시 하는 HTML 폼에 XSRF 토큰을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-169">The [TokenProvider service](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="58994-170">`MainLayout`구성 요소 (*Shared/mainlayout. razor*)에서 `LoginDisplay` 구성 요소를 상위 행 요소 내용에 추가 합니다 `<div>` .</span><span class="sxs-lookup"><span data-stu-id="58994-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="58994-171">스타일 인증 끝점</span><span class="sxs-lookup"><span data-stu-id="58994-171">Style authentication endpoints</span></span>

<span data-ttu-id="58994-172">Blazor서버에서는 페이지 Razor Identity 페이지를 사용 하므로 방문자가 페이지와 구성 요소 간을 탐색할 때 UI의 스타일이 변경 Identity 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="58994-173">Incongruous 스타일을 해결 하는 두 가지 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="58994-174">빌드 Identity 구성 요소</span><span class="sxs-lookup"><span data-stu-id="58994-174">Build Identity components</span></span>

<span data-ttu-id="58994-175">페이지 대신 구성 요소를 사용 하는 방법은 Identity 구성 요소를 빌드하는 것입니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="58994-176">`SignInManager`및 `UserManager` 는 구성 요소에서 지원 되지 않기 때문에 Razor 서버 앱에서 API 끝점을 사용 Blazor 하 여 사용자 계정 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="58994-177">앱 스타일을 사용 하 여 사용자 지정 레이아웃 사용 Blazor</span><span class="sxs-lookup"><span data-stu-id="58994-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="58994-178">Identity페이지 레이아웃 및 스타일을 수정 하 여 기본 테마를 사용 하는 페이지를 만들 수 있습니다 Blazor .</span><span class="sxs-lookup"><span data-stu-id="58994-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="58994-179">이 섹션의 예제는 단순히 사용자 지정을 위한 출발점입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="58994-180">최상의 사용자 환경을 위해 추가 작업이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="58994-181">새 `NavMenu_IdentityLayout` 구성 요소 (*Shared/NavMenu_IdentityLayout razor*)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="58994-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="58994-182">구성 요소의 태그와 코드에 대해 앱 `NavMenu` 구성 요소의 동일한 콘텐츠 (*Shared/NavMenu*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="58994-183">`NavLink` `RedirectToLogin` 구성 요소의 자동 리디렉션이 인증 또는 권한 부여가 필요한 구성 요소에 대해 실패 하므로 익명으로 연결할 수 없는 구성 요소에 대 한 모든를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="58994-184">*Pages/Shared/Layout. cshtml* 파일에서 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="58994-185">Razor태그 도우미와 앱의 구성 요소를 *공유* 폴더에 사용 하려면 파일의 맨 위에 지시문을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="58994-186">를 `{APPLICATION ASSEMBLY}` 앱의 어셈블리 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="58994-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="58994-187">`<base>`콘텐츠에 태그 및 Blazor 스타일 시트를 추가 `<link>` 합니다 `<head>` .</span><span class="sxs-lookup"><span data-stu-id="58994-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="58994-188">태그의 내용을 `<body>` 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="58994-189">Identity Blazor 권한 부여를 사용 하 여 서버 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="58994-190">일부 Identity 옵션은 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-191">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="58994-192">전체 Identity UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="58994-192">Create full Identity UI source</span></span>

<span data-ttu-id="58994-193">UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 Identity 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="58994-194">다음 강조 표시 된 코드는 Identity Identity ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="58994-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="58994-195">이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="58994-196">기본값은 Identity 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="58994-197">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="58994-198">구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="58994-199">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="58994-199">Disable register page</span></span>

<span data-ttu-id="58994-200">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="58994-200">To disable user registration:</span></span>

* <span data-ttu-id="58994-201">스 캐 폴드 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-201">Scaffold Identity.</span></span> <span data-ttu-id="58994-202">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-202">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="58994-203">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-203">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="58994-204">사용자가이 끝점에서 등록할 수 없도록 *영역/ Identity /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-204">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="58994-205">이전 변경 내용과 일치 하도록 *영역/ Identity /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-205">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="58994-206">*영역/ Identity /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-206">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="58994-207">*영역/ Identity /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-207">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="58994-208">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-208">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="58994-209">에서 확인 코드를 제거 합니다 `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="58994-209">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="58994-210">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="58994-210">Use another app to add users</span></span>

<span data-ttu-id="58994-211">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-211">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="58994-212">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-212">Options to add users include:</span></span>

* <span data-ttu-id="58994-213">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-213">A dedicated admin web app.</span></span>
* <span data-ttu-id="58994-214">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-214">A console app.</span></span>

<span data-ttu-id="58994-215">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-215">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="58994-216">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-216">A list of users is read into memory.</span></span>
* <span data-ttu-id="58994-217">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-217">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="58994-218">사용자가 데이터베이스에 추가 됩니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-218">The user is added to the Identity database.</span></span>
* <span data-ttu-id="58994-219">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-219">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="58994-220">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-220">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="58994-221">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-221">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="58994-222">정적 자산 게시 방지 Identity</span><span class="sxs-lookup"><span data-stu-id="58994-222">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="58994-223">정적 Identity 자산을 웹 루트에 게시 하지 않으려면를 참조 하십시오 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="58994-223">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58994-224">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="58994-224">Additional resources</span></span>

* [<span data-ttu-id="58994-225">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="58994-225">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58994-226">ASP.NET Core 2.1 이상에서는 [ASP.NET Core Identity ](xref:security/authentication/identity) 를 [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-226">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="58994-227">를 포함 하는 응용 프로그램 Identity 은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-227">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="58994-228">코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-228">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="58994-229">예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-229">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="58994-230">생성된 코드는 Identity RCL의 동일한 코드보다 우선합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-230">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="58994-231">기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-231">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="58994-232">인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-232">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="58994-233">선택한 Identity 코드의 옵션이 생성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-233">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="58994-234">스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-234">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="58994-235">이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-235">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="58994-236">스 캐 폴더를 Identity 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme* 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="58994-236">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="58994-237">*ScaffoldingReadme* 파일은 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-237">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="58994-238">이 문서에는 *ScaffoldingReadme* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-238">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="58994-239">파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-239">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="58994-240">스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-240">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="58994-241">서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-241">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="58994-242">스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-242">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="58994-243">이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-243">Services to enable these features must be added manually.</span></span> <span data-ttu-id="58994-244">예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-244">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="58994-245">Identity빈 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-245">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="58994-246">클래스에 다음의 강조 표시 된 호출을 추가 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="58994-246">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="58994-247">Identity Razor 기존 권한 부여 없이 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-247">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="58994-248">는 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-248"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-249">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-249">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="58994-250">마이그레이션, UseAuthentication 및 레이아웃</span><span class="sxs-lookup"><span data-stu-id="58994-250">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="58994-251">인증 사용</span><span class="sxs-lookup"><span data-stu-id="58994-251">Enable authentication</span></span>

<span data-ttu-id="58994-252">`Configure`클래스의 메서드에서 다음 `Startup` 이후에 [useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다 `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="58994-252">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="58994-253">레이아웃 변경</span><span class="sxs-lookup"><span data-stu-id="58994-253">Layout changes</span></span>

<span data-ttu-id="58994-254">선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-254">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="58994-255">Identity Razor 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-255">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="58994-256">일부 Identity 옵션은 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-256">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-257">자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-257">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="58994-258">Identity기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-258">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="58994-259">선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-259">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="58994-260">*Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*</span><span class="sxs-lookup"><span data-stu-id="58994-260">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="58994-261">는 *영역/ Identity /IdentityHostingStartup.cs*구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-261"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="58994-262">자세한 내용은 IHostingStartup를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="58994-262">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="58994-263">다음 이후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 호출 `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="58994-263">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="58994-264">Identity권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드</span><span class="sxs-lookup"><span data-stu-id="58994-264">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="58994-265">*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-265">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="58994-266">전체 Identity UI 원본 만들기</span><span class="sxs-lookup"><span data-stu-id="58994-266">Create full Identity UI source</span></span>

<span data-ttu-id="58994-267">UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 Identity 고 **모든 파일 재정의**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-267">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="58994-268">다음 강조 표시 된 코드는 Identity Identity ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="58994-268">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="58994-269">이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-269">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="58994-270">기본값은 Identity 다음 코드에서 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-270">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="58994-271">다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-271">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="58994-272">구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-272">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="58994-273">등록 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="58994-273">Disable register page</span></span>

<span data-ttu-id="58994-274">사용자 등록을 사용 하지 않도록 설정 하려면:</span><span class="sxs-lookup"><span data-stu-id="58994-274">To disable user registration:</span></span>

* <span data-ttu-id="58994-275">스 캐 폴드 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-275">Scaffold Identity.</span></span> <span data-ttu-id="58994-276">Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-276">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="58994-277">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-277">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="58994-278">사용자가이 끝점에서 등록할 수 없도록 *영역/ Identity /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-278">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="58994-279">이전 변경 내용과 일치 하도록 *영역/ Identity /Pages/Account/Register.cshtml* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-279">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="58994-280">*영역/ Identity /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-280">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="58994-281">*영역/ Identity /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-281">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="58994-282">Cshtml 파일에서 코드 및 링크를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-282">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="58994-283">에서 확인 코드를 제거 합니다 `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="58994-283">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="58994-284">다른 앱을 사용 하 여 사용자 추가</span><span class="sxs-lookup"><span data-stu-id="58994-284">Use another app to add users</span></span>

<span data-ttu-id="58994-285">웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-285">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="58994-286">사용자를 추가 하는 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-286">Options to add users include:</span></span>

* <span data-ttu-id="58994-287">전용 관리 웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-287">A dedicated admin web app.</span></span>
* <span data-ttu-id="58994-288">콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="58994-288">A console app.</span></span>

<span data-ttu-id="58994-289">다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-289">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="58994-290">사용자 목록을 메모리로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-290">A list of users is read into memory.</span></span>
* <span data-ttu-id="58994-291">각 사용자에 대해 강력한 고유 암호가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-291">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="58994-292">사용자가 데이터베이스에 추가 됩니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="58994-292">The user is added to the Identity database.</span></span>
* <span data-ttu-id="58994-293">사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="58994-293">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="58994-294">다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="58994-294">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="58994-295">프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="58994-295">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58994-296">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="58994-296">Additional resources</span></span>

* [<span data-ttu-id="58994-297">ASP.NET Core 2.1 이상으로 인증 코드 변경</span><span class="sxs-lookup"><span data-stu-id="58994-297">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
