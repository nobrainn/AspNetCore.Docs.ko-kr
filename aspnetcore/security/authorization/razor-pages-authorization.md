---
title: RazorASP.NET Core의 페이지 권한 부여 규칙
author: rick-anderson
description: 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙으로 페이지에 대 한 액세스를 제어 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0492dd3d9b2aee7e844e944bea96259c3ddf18d0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408723"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="f4714-103">ASP.NET Core의 페이지 권한 부여 규칙</span><span class="sxs-lookup"><span data-stu-id="f4714-103"> Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4714-104">페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="f4714-105">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="f4714-106">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="f4714-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4714-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4714-108">샘플 앱은 [ASP.NET Core Identity 하지 않고 쿠키 인증 ](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="f4714-109">이 항목에 표시 된 개념과 예제는 ASP.NET Core를 사용 하는 앱에 동일 하 게 적용 Identity 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="f4714-110">ASP.NET Core를 사용 하려면 Identity 의 지침을 따르세요 <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="f4714-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="f4714-111">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-111">Require authorization to access a page</span></span>

<span data-ttu-id="f4714-112">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="f4714-113">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="f4714-114">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="f4714-115">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="f4714-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="f4714-116">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f4714-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="f4714-117">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="f4714-118">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="f4714-119">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="f4714-120">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="f4714-121">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-121">Require authorization to access an area page</span></span>

<span data-ttu-id="f4714-122">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="f4714-123">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f4714-124">예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="f4714-125">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="f4714-126">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="f4714-127">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="f4714-128">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f4714-129">예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리*입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="f4714-130">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="f4714-131">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="f4714-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="f4714-132">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="f4714-133">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="f4714-134">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="f4714-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="f4714-135">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="f4714-136">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="f4714-137">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="f4714-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="f4714-138">페이지의 폴더에 권한 부여가 필요 하다 고 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="f4714-139">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="f4714-140">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="f4714-141">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="f4714-142"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4714-143">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f4714-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4714-144">페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="f4714-145">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="f4714-146">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="f4714-147">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4714-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4714-148">샘플 앱은 [ASP.NET Core Identity 하지 않고 쿠키 인증 ](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="f4714-149">이 항목에 표시 된 개념과 예제는 ASP.NET Core를 사용 하는 앱에 동일 하 게 적용 Identity 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="f4714-150">ASP.NET Core를 사용 하려면 Identity 의 지침을 따르세요 <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="f4714-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="f4714-151">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-151">Require authorization to access a page</span></span>

<span data-ttu-id="f4714-152">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="f4714-153">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="f4714-154">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="f4714-155">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="f4714-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="f4714-156">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f4714-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="f4714-157">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="f4714-158">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="f4714-159">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="f4714-160">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="f4714-161">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-161">Require authorization to access an area page</span></span>

<span data-ttu-id="f4714-162">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="f4714-163">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f4714-164">예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="f4714-165">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="f4714-166">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="f4714-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="f4714-167">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="f4714-168">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f4714-169">예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리*입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="f4714-170">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="f4714-171">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="f4714-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="f4714-172">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="f4714-173">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="f4714-174">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="f4714-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="f4714-175">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="f4714-176">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="f4714-177">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="f4714-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="f4714-178">권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="f4714-179">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="f4714-180">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="f4714-181">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="f4714-182"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="f4714-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4714-183">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f4714-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
