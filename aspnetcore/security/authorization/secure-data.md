---
title: 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기
author: rick-anderson
description: Razor권한 부여로 보호 되는 사용자 데이터를 사용 하 여 페이지 앱을 만드는 방법을 알아봅니다. HTTPS, 인증, 보안 ASP.NET Core를 포함 Identity 합니다.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f50015af864a4a62abd5e2eab508aac915cb6370
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404719"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="7e6db-104">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="7e6db-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7e6db-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="7e6db-106">ASP.NET Core MVC 버전은 [이 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7e6db-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="7e6db-107">이 자습서의 ASP.NET Core 1.1 버전은 [이](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="7e6db-108">1.1 ASP.NET Core 샘플은 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7e6db-109">[이 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) 보기</span><span class="sxs-lookup"><span data-stu-id="7e6db-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e6db-110">이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="7e6db-111">사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="7e6db-112">다음 세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-112">There are three security groups:</span></span>

* <span data-ttu-id="7e6db-113">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="7e6db-114">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="7e6db-115">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="7e6db-116">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="7e6db-117">이 문서의 이미지는 최신 템플릿과 정확히 일치 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="7e6db-118">다음 이미지에서는 사용자 Rick ( `rick@example.com` )가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="7e6db-119">Rick는 승인 된 연락처만 볼 수 **있으며** / **Delete Delete**는 / 연락처에 대 한 새 링크를**만듭니다** .</span><span class="sxs-lookup"><span data-stu-id="7e6db-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="7e6db-120">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="7e6db-121">관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="7e6db-123">다음 이미지에서 `manager@contoso.com` 는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인을 보여 주는 스크린샷 manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="7e6db-125">다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-125">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자 보기](secure-data/_static/manager.png)

<span data-ttu-id="7e6db-127">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="7e6db-128">다음 이미지에서 `admin@contoso.com` 는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인을 보여 주는 스크린샷 admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="7e6db-130">관리자에 게는 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-130">The administrator has all privileges.</span></span> <span data-ttu-id="7e6db-131">연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="7e6db-132">다음 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 앱을 만들었습니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="7e6db-133">이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="7e6db-134">`ContactIsOwnerAuthorizationHandler`: 사용자가 해당 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="7e6db-135">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="7e6db-136">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e6db-137">필수 조건</span><span class="sxs-lookup"><span data-stu-id="7e6db-137">Prerequisites</span></span>

<span data-ttu-id="7e6db-138">이 자습서는 고급입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-138">This tutorial is advanced.</span></span> <span data-ttu-id="7e6db-139">다음에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-139">You should be familiar with:</span></span>

* [<span data-ttu-id="7e6db-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e6db-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="7e6db-141">인증</span><span class="sxs-lookup"><span data-stu-id="7e6db-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7e6db-142">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="7e6db-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7e6db-143">권한 부여</span><span class="sxs-lookup"><span data-stu-id="7e6db-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="7e6db-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7e6db-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="7e6db-145">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="7e6db-145">The starter and completed app</span></span>

<span data-ttu-id="7e6db-146">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="7e6db-147">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="7e6db-148">시작 앱</span><span class="sxs-lookup"><span data-stu-id="7e6db-148">The starter app</span></span>

<span data-ttu-id="7e6db-149">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="7e6db-150">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="7e6db-151">보안 사용자 데이터</span><span class="sxs-lookup"><span data-stu-id="7e6db-151">Secure user data</span></span>

<span data-ttu-id="7e6db-152">다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="7e6db-153">완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="7e6db-154">사용자에 게 연락처 데이터 연결</span><span class="sxs-lookup"><span data-stu-id="7e6db-154">Tie the contact data to the user</span></span>

<span data-ttu-id="7e6db-155">ASP.NET [Identity](xref:security/authentication/identity) 사용자 ID를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="7e6db-156">`OwnerID` `ContactStatus` 모델에 및을 추가 합니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="7e6db-157">`OwnerID``AspNetUser`데이터베이스에 있는 테이블의 사용자 ID입니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7e6db-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="7e6db-158">`Status`필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="7e6db-159">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="7e6db-160">역할 서비스 추가Identity</span><span class="sxs-lookup"><span data-stu-id="7e6db-160">Add Role services to Identity</span></span>

<span data-ttu-id="7e6db-161">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="7e6db-162">인증 된 사용자 필요</span><span class="sxs-lookup"><span data-stu-id="7e6db-162">Require authenticated users</span></span>

<span data-ttu-id="7e6db-163">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="7e6db-164">특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다 `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="7e6db-165">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 하면 새로 추가 된 Razor 페이지와 컨트롤러를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="7e6db-166">기본적으로 인증을 요구 하는 것은 특성을 포함 하는 새 컨트롤러 및 페이지에 의존 하는 것 보다 안전 Razor 합니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="7e6db-167">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스 및 개인 정보 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="7e6db-168">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="7e6db-168">Configure the test account</span></span>

<span data-ttu-id="7e6db-169">`SeedData`클래스는 두 개의 계정 즉, 관리자와 관리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="7e6db-170">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="7e6db-171">프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="7e6db-172">강력한 암호를 지정 하지 않으면가 호출 될 때 예외가 throw 됩니다 `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="7e6db-173">`Main`테스트 암호를 사용 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="7e6db-174">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="7e6db-175">`Initialize`클래스의 메서드를 업데이트 `SeedData` 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="7e6db-176">관리자 사용자 ID와 `ContactStatus` 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="7e6db-177">연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="7e6db-178">모든 연락처에 사용자 ID 및 상태를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="7e6db-179">하나의 연락처만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="7e6db-180">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="7e6db-181">`ContactIsOwnerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7e6db-182">는 `ContactIsOwnerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="7e6db-183">`ContactIsOwnerAuthorizationHandler`호출 [컨텍스트입니다. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="7e6db-184">권한 부여 처리기 일반적:</span><span class="sxs-lookup"><span data-stu-id="7e6db-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="7e6db-185">`context.Succeed`요구 사항이 충족 되 면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="7e6db-186">`Task.CompletedTask`요구 사항이 충족 되지 않으면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="7e6db-187">`Task.CompletedTask`은 (는) 성공 또는 실패 하지 않으므로 &mdash; 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="7e6db-188">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="7e6db-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="7e6db-189">앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="7e6db-190">`ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="7e6db-191">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-191">Create a manager authorization handler</span></span>

<span data-ttu-id="7e6db-192">`ContactManagerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7e6db-193">는 `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="7e6db-194">관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="7e6db-195">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="7e6db-196">`ContactAdministratorsAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7e6db-197">는 `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="7e6db-198">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="7e6db-199">권한 부여 처리기 등록</span><span class="sxs-lookup"><span data-stu-id="7e6db-199">Register the authorization handlers</span></span>

<span data-ttu-id="7e6db-200">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="7e6db-201">는 `ContactIsOwnerAuthorizationHandler` Entity Framework Core를 기반으로 하는 ASP.NET Core을 사용 합니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7e6db-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="7e6db-202">종속성 주입을 통해에 사용할 수 있도록 서비스 컬렉션에 처리기를 `ContactsController` 등록 [dependency injection](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e6db-203">끝에 다음 코드를 추가 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="7e6db-204">`ContactAdministratorsAuthorizationHandler`및 `ContactManagerAuthorizationHandler` 는 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="7e6db-205">이는 EF를 사용 하지 않고 필요한 모든 정보가 메서드의 매개 변수에 단일 항목 때문에 발생 `Context` `HandleRequirementAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="7e6db-206">인증 지원</span><span class="sxs-lookup"><span data-stu-id="7e6db-206">Support authorization</span></span>

<span data-ttu-id="7e6db-207">이 섹션에서는 페이지를 업데이트 하 Razor 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="7e6db-208">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="7e6db-209">클래스를 검토 `ContactOperations` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="7e6db-210">이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="7e6db-211">연락처 페이지에 대 한 기본 클래스 만들기 Razor</span><span class="sxs-lookup"><span data-stu-id="7e6db-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="7e6db-212">연락처 페이지에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7e6db-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="7e6db-213">기본 클래스는 초기화 코드를 한 위치에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="7e6db-214">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="7e6db-214">The preceding code:</span></span>

* <span data-ttu-id="7e6db-215">`IAuthorizationService`권한 부여 처리기에 액세스 하는 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="7e6db-216">서비스를 추가 Identity `UserManager` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="7e6db-217">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="7e6db-218">CreateModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-218">Update the CreateModel</span></span>

<span data-ttu-id="7e6db-219">기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다 `DI_BasePageModel` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="7e6db-220">메서드를 `CreateModel.OnPostAsync` 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="7e6db-221">모델에 사용자 ID를 추가 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="7e6db-222">권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="7e6db-223">IndexModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-223">Update the IndexModel</span></span>

<span data-ttu-id="7e6db-224">`OnGetAsync`승인 된 연락처만 일반 사용자에 게 표시 되도록 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="7e6db-225">EditModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-225">Update the EditModel</span></span>

<span data-ttu-id="7e6db-226">사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="7e6db-227">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="7e6db-228">특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="7e6db-229">리소스 기반 권한 부여는 필수적 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="7e6db-230">앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="7e6db-231">리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="7e6db-232">DeleteModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-232">Update the DeleteModel</span></span>

<span data-ttu-id="7e6db-233">권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="7e6db-234">권한 부여 서비스를 뷰에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="7e6db-235">현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="7e6db-236">모든 보기에서 사용할 수 있도록 *Pages/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="7e6db-237">위의 태그는 여러 문을 추가 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="7e6db-238">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="7e6db-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="7e6db-239">데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="7e6db-240">링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="7e6db-241">사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="7e6db-242">Razor페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="7e6db-243">업데이트 세부 정보</span><span class="sxs-lookup"><span data-stu-id="7e6db-243">Update Details</span></span>

<span data-ttu-id="7e6db-244">관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="7e6db-245">세부 정보 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="7e6db-246">역할에 사용자 추가 또는 제거</span><span class="sxs-lookup"><span data-stu-id="7e6db-246">Add or remove a user to a role</span></span>

<span data-ttu-id="7e6db-247">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7e6db-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="7e6db-248">사용자가 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-248">Removing privileges from a user.</span></span> <span data-ttu-id="7e6db-249">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="7e6db-250">사용자에 게 권한 추가</span><span class="sxs-lookup"><span data-stu-id="7e6db-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="7e6db-251">챌린지와 금지 간의 차이점</span><span class="sxs-lookup"><span data-stu-id="7e6db-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="7e6db-252">이 앱은 인증 된 사용자를 [요구](#require-authenticated-users)하도록 기본 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="7e6db-253">다음 코드는 익명 사용자를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-253">The following code allows anonymous users.</span></span> <span data-ttu-id="7e6db-254">익명 사용자는 챌린지 vs 금지 간의 차이점을 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="7e6db-255">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="7e6db-255">In the preceding code:</span></span>

* <span data-ttu-id="7e6db-256">사용자가 인증 **되지 않은** 경우 `ChallengeResult` 이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="7e6db-257">가 반환 되 면 `ChallengeResult` 사용자가 로그인 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="7e6db-258">사용자가 인증 되었지만 권한이 부여 되지 않은 경우 `ForbidResult` 이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="7e6db-259">가 반환 되 면 `ForbidResult` 사용자가 액세스 거부 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="7e6db-260">완성 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="7e6db-260">Test the completed app</span></span>

<span data-ttu-id="7e6db-261">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="7e6db-262">강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="7e6db-263">예를 들어 `Passw0rd!` 은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="7e6db-264">프로젝트의 폴더에서 다음 명령을 실행 합니다 `<PW>` . 여기서은 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="7e6db-265">앱에 연락처가 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="7e6db-265">If the app has contacts:</span></span>

* <span data-ttu-id="7e6db-266">테이블의 모든 레코드를 삭제 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="7e6db-267">응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="7e6db-268">완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="7e6db-269">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="7e6db-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="7e6db-270">다른 사용자로 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="7e6db-271">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-271">Verify the following operations:</span></span>

* <span data-ttu-id="7e6db-272">등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="7e6db-273">등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="7e6db-274">관리자는 연락처 데이터를 승인/거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="7e6db-275">`Details`보기는 **승인** 및 **거부** 단추를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="7e6db-276">관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="7e6db-277">사용자</span><span class="sxs-lookup"><span data-stu-id="7e6db-277">User</span></span>                | <span data-ttu-id="7e6db-278">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="7e6db-278">Seeded by the app</span></span> | <span data-ttu-id="7e6db-279">옵션</span><span class="sxs-lookup"><span data-stu-id="7e6db-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="7e6db-280">No</span><span class="sxs-lookup"><span data-stu-id="7e6db-280">No</span></span>                | <span data-ttu-id="7e6db-281">자신의 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="7e6db-282">Yes</span><span class="sxs-lookup"><span data-stu-id="7e6db-282">Yes</span></span>               | <span data-ttu-id="7e6db-283">자신의 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="7e6db-284">Yes</span><span class="sxs-lookup"><span data-stu-id="7e6db-284">Yes</span></span>               | <span data-ttu-id="7e6db-285">모든 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="7e6db-286">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="7e6db-287">관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="7e6db-288">이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="7e6db-289">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-289">Create the starter app</span></span>

* <span data-ttu-id="7e6db-290">Razor"연락처 관리자" 라는 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="7e6db-291">**개별 사용자 계정을**사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="7e6db-292">네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="7e6db-293">`-uld`SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="7e6db-294">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="7e6db-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="7e6db-295">모델을 스 캐 폴드 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="7e6db-296">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="7e6db-297">명령을 사용 하 여 버그를 발생 하는 경우 `dotnet aspnet-codegenerator razorpage` [이 GitHub 문제](https://github.com/aspnet/Scaffolding/issues/984)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7e6db-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="7e6db-298">*Pages/Shared/_Layout cshtml* 파일에서 지 각를 **업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="7e6db-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="7e6db-299">연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="7e6db-300">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="7e6db-300">Seed the database</span></span>

<span data-ttu-id="7e6db-301">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="7e6db-302">`SeedData.Initialize`다음에서 호출 `Main` :</span><span class="sxs-lookup"><span data-stu-id="7e6db-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="7e6db-303">응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-303">Test that the app seeded the database.</span></span> <span data-ttu-id="7e6db-304">Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="7e6db-305">이 자습서에서는 권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 웹 앱을 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="7e6db-306">사용자가 만든 인증 (등록) 된 연락처의 목록이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="7e6db-307">다음 세 가지 보안 그룹이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-307">There are three security groups:</span></span>

* <span data-ttu-id="7e6db-308">**등록 된 사용자** 는 승인 된 모든 데이터를 볼 수 있으며 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="7e6db-309">**관리자** 는 연락처 데이터를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="7e6db-310">승인 된 연락처만 사용자에 게 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="7e6db-311">**관리자** 는 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="7e6db-312">다음 이미지에서는 사용자 Rick ( `rick@example.com` )가 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="7e6db-313">Rick는 승인 된 연락처만 볼 수 **있으며** / **Delete Delete**는 / 연락처에 대 한 새 링크를**만듭니다** .</span><span class="sxs-lookup"><span data-stu-id="7e6db-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="7e6db-314">Rick에서 만든 마지막 레코드만 **편집** 및 **삭제** 링크를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="7e6db-315">관리자 또는 관리자가 상태를 "승인 됨"으로 변경할 때까지 다른 사용자는 마지막 레코드를 볼 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick 로그인을 보여 주는 스크린샷](secure-data/_static/rick.png)

<span data-ttu-id="7e6db-317">다음 이미지에서 `manager@contoso.com` 는 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![로그인을 보여 주는 스크린샷 manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="7e6db-319">다음 그림은 연락처의 관리자 세부 정보 보기를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-319">The following image shows the managers details view of a contact:</span></span>

![연락처의 관리자 보기](secure-data/_static/manager.png)

<span data-ttu-id="7e6db-321">**승인** 및 **거부** 단추는 관리자와 관리자 에게만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="7e6db-322">다음 이미지에서 `admin@contoso.com` 는 및 관리자 역할에 로그인 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![로그인을 보여 주는 스크린샷 admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="7e6db-324">관리자에 게는 모든 권한이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-324">The administrator has all privileges.</span></span> <span data-ttu-id="7e6db-325">연락처를 읽고 편집/삭제 하 고 연락처의 상태를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="7e6db-326">다음 모델을 [스 캐 폴딩](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 하 여 앱을 만들었습니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="7e6db-327">이 샘플에는 다음과 같은 권한 부여 처리기가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="7e6db-328">`ContactIsOwnerAuthorizationHandler`: 사용자가 해당 데이터만 편집할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="7e6db-329">`ContactManagerAuthorizationHandler`: 관리자가 연락처를 승인 하거나 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="7e6db-330">`ContactAdministratorsAuthorizationHandler`: 관리자가 연락처를 승인 또는 거부 하 고 연락처를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e6db-331">필수 조건</span><span class="sxs-lookup"><span data-stu-id="7e6db-331">Prerequisites</span></span>

<span data-ttu-id="7e6db-332">이 자습서는 고급입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-332">This tutorial is advanced.</span></span> <span data-ttu-id="7e6db-333">다음에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-333">You should be familiar with:</span></span>

* [<span data-ttu-id="7e6db-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e6db-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="7e6db-335">인증</span><span class="sxs-lookup"><span data-stu-id="7e6db-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7e6db-336">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="7e6db-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7e6db-337">권한 부여</span><span class="sxs-lookup"><span data-stu-id="7e6db-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="7e6db-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7e6db-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="7e6db-339">시작 및 완료 된 앱</span><span class="sxs-lookup"><span data-stu-id="7e6db-339">The starter and completed app</span></span>

<span data-ttu-id="7e6db-340">[완성](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) 된 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="7e6db-341">보안 기능에 익숙해질 수 있도록 완성 된 앱을 [테스트](#test-the-completed-app) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="7e6db-342">시작 앱</span><span class="sxs-lookup"><span data-stu-id="7e6db-342">The starter app</span></span>

<span data-ttu-id="7e6db-343">[시작](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) 앱을 [다운로드](xref:index#how-to-download-a-sample) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="7e6db-344">앱을 실행 하 고, 연락처 **관리자** 링크를 탭 하 고, 연락처를 만들고, 편집 하 고, 삭제할 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="7e6db-345">보안 사용자 데이터</span><span class="sxs-lookup"><span data-stu-id="7e6db-345">Secure user data</span></span>

<span data-ttu-id="7e6db-346">다음 섹션에는 보안 사용자 데이터 앱을 만들기 위한 모든 주요 단계가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="7e6db-347">완료 된 프로젝트를 참조 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="7e6db-348">사용자에 게 연락처 데이터 연결</span><span class="sxs-lookup"><span data-stu-id="7e6db-348">Tie the contact data to the user</span></span>

<span data-ttu-id="7e6db-349">ASP.NET [Identity](xref:security/authentication/identity) 사용자 ID를 사용 하 여 사용자가 데이터를 편집할 수 있지만 다른 사용자 데이터는 편집할 수 없도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="7e6db-350">`OwnerID` `ContactStatus` 모델에 및을 추가 합니다 `Contact` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="7e6db-351">`OwnerID``AspNetUser`데이터베이스에 있는 테이블의 사용자 ID입니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7e6db-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="7e6db-352">`Status`필드는 일반 사용자가 연락처를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="7e6db-353">새 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="7e6db-354">역할 서비스 추가Identity</span><span class="sxs-lookup"><span data-stu-id="7e6db-354">Add Role services to Identity</span></span>

<span data-ttu-id="7e6db-355">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="7e6db-356">인증 된 사용자 필요</span><span class="sxs-lookup"><span data-stu-id="7e6db-356">Require authenticated users</span></span>

<span data-ttu-id="7e6db-357">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="7e6db-358">특성을 사용 하 여 Razor 페이지, 컨트롤러 또는 작업 메서드 수준에서 인증을 옵트아웃 (opt out) 할 수 있습니다 `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="7e6db-359">사용자를 인증 하도록 요구 하는 기본 인증 정책을 설정 하면 새로 추가 된 Razor 페이지와 컨트롤러를 보호 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="7e6db-360">기본적으로 인증을 요구 하는 것은 특성을 포함 하는 새 컨트롤러 및 페이지에 의존 하는 것 보다 안전 Razor 합니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="7e6db-361">익명 사용자가 등록 하기 전에 사이트에 대 한 정보를 얻을 수 있도록 인덱스, 정보 및 연락처 페이지에 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="7e6db-362">테스트 계정 구성</span><span class="sxs-lookup"><span data-stu-id="7e6db-362">Configure the test account</span></span>

<span data-ttu-id="7e6db-363">`SeedData`클래스는 두 개의 계정 즉, 관리자와 관리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="7e6db-364">암호 [관리자 도구](xref:security/app-secrets) 를 사용 하 여 이러한 계정에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="7e6db-365">프로젝트 디렉터리 ( *Program.cs*가 포함 된 디렉터리)에서 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="7e6db-366">강력한 암호를 지정 하지 않으면가 호출 될 때 예외가 throw 됩니다 `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="7e6db-367">`Main`테스트 암호를 사용 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="7e6db-368">테스트 계정을 만들고 연락처를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="7e6db-369">`Initialize`클래스의 메서드를 업데이트 `SeedData` 하 여 테스트 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="7e6db-370">관리자 사용자 ID와 `ContactStatus` 연락처를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="7e6db-371">연락처 "제출 됨" 및 "거부 됨" 중 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="7e6db-372">모든 연락처에 사용자 ID 및 상태를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="7e6db-373">하나의 연락처만 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="7e6db-374">소유자, 관리자 및 관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="7e6db-375">*권한 부여* 폴더를 만들고 `ContactIsOwnerAuthorizationHandler` 여기에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="7e6db-376">는 `ContactIsOwnerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 리소스를 소유 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="7e6db-377">`ContactIsOwnerAuthorizationHandler`호출 [컨텍스트입니다. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)현재 인증 된 사용자가 연락처 소유자 인 경우 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="7e6db-378">권한 부여 처리기 일반적:</span><span class="sxs-lookup"><span data-stu-id="7e6db-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="7e6db-379">`context.Succeed`요구 사항이 충족 되 면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="7e6db-380">`Task.CompletedTask`요구 사항이 충족 되지 않으면를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="7e6db-381">`Task.CompletedTask`은 (는) 성공 또는 실패 하지 않으므로 &mdash; 다른 권한 부여 처리기를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="7e6db-382">명시적으로 실패 해야 하는 경우 컨텍스트를 반환 [합니다. 실패](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="7e6db-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="7e6db-383">앱에서 연락처 소유자는 자신의 데이터를 편집/삭제/만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="7e6db-384">`ContactIsOwnerAuthorizationHandler`는 요구 사항 매개 변수에 전달 된 작업을 확인할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="7e6db-385">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-385">Create a manager authorization handler</span></span>

<span data-ttu-id="7e6db-386">`ContactManagerAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7e6db-387">는 `ContactManagerAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="7e6db-388">관리자만 콘텐츠 변경 내용 (신규 또는 변경 됨)을 승인 또는 거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="7e6db-389">관리자 권한 부여 처리기 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="7e6db-390">`ContactAdministratorsAuthorizationHandler` *권한 부여* 폴더에 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7e6db-391">는 `ContactAdministratorsAuthorizationHandler` 리소스에 대해 작동 하는 사용자가 관리자 인지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="7e6db-392">관리자는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="7e6db-393">권한 부여 처리기 등록</span><span class="sxs-lookup"><span data-stu-id="7e6db-393">Register the authorization handlers</span></span>

<span data-ttu-id="7e6db-394">[Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)를 사용 하 여 [종속성 주입](xref:fundamentals/dependency-injection) 을 위해 Entity Framework Core를 사용 하는 서비스를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="7e6db-395">는 `ContactIsOwnerAuthorizationHandler` Entity Framework Core를 기반으로 하는 ASP.NET Core을 사용 합니다 [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="7e6db-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="7e6db-396">종속성 주입을 통해에 사용할 수 있도록 서비스 컬렉션에 처리기를 `ContactsController` 등록 [dependency injection](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e6db-397">끝에 다음 코드를 추가 합니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="7e6db-398">`ContactAdministratorsAuthorizationHandler`및 `ContactManagerAuthorizationHandler` 는 단일 항목로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="7e6db-399">이는 EF를 사용 하지 않고 필요한 모든 정보가 메서드의 매개 변수에 단일 항목 때문에 발생 `Context` `HandleRequirementAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="7e6db-400">인증 지원</span><span class="sxs-lookup"><span data-stu-id="7e6db-400">Support authorization</span></span>

<span data-ttu-id="7e6db-401">이 섹션에서는 페이지를 업데이트 하 Razor 고 작업 요구 사항 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="7e6db-402">연락처 작업 요구 사항 클래스를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="7e6db-403">클래스를 검토 `ContactOperations` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="7e6db-404">이 클래스에는 앱이 지 원하는 요구 사항이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="7e6db-405">연락처 페이지에 대 한 기본 클래스 만들기 Razor</span><span class="sxs-lookup"><span data-stu-id="7e6db-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="7e6db-406">연락처 페이지에 사용 되는 서비스를 포함 하는 기본 클래스를 만듭니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="7e6db-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="7e6db-407">기본 클래스는 초기화 코드를 한 위치에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="7e6db-408">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="7e6db-408">The preceding code:</span></span>

* <span data-ttu-id="7e6db-409">`IAuthorizationService`권한 부여 처리기에 액세스 하는 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="7e6db-410">서비스를 추가 Identity `UserManager` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="7e6db-411">`ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="7e6db-412">CreateModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-412">Update the CreateModel</span></span>

<span data-ttu-id="7e6db-413">기본 클래스를 사용 하도록 create page model 생성자를 업데이트 합니다 `DI_BasePageModel` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="7e6db-414">메서드를 `CreateModel.OnPostAsync` 다음으로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="7e6db-415">모델에 사용자 ID를 추가 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="7e6db-416">권한 부여 처리기를 호출 하 여 사용자에 게 연락처를 만들 수 있는 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="7e6db-417">IndexModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-417">Update the IndexModel</span></span>

<span data-ttu-id="7e6db-418">`OnGetAsync`승인 된 연락처만 일반 사용자에 게 표시 되도록 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="7e6db-419">EditModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-419">Update the EditModel</span></span>

<span data-ttu-id="7e6db-420">사용자가 연락처를 소유 하 고 있는지 확인 하기 위해 권한 부여 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="7e6db-421">리소스 권한 부여의 유효성을 검사 하는 중이기 때문에 `[Authorize]` 특성에 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="7e6db-422">특성이 평가 될 때 앱에서 리소스에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="7e6db-423">리소스 기반 권한 부여는 필수적 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="7e6db-424">앱이 리소스에 대 한 액세스 권한이 있는 경우 페이지 모델에서 로드 하거나 처리기 자체 내에서 로드 하 여 검사를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="7e6db-425">리소스 키를 전달 하 여 리소스에 자주 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="7e6db-426">DeleteModel 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-426">Update the DeleteModel</span></span>

<span data-ttu-id="7e6db-427">권한 부여 처리기를 사용 하 여 사용자에 게 연락처에 대 한 삭제 권한이 있는지 확인 하기 위해 삭제 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="7e6db-428">권한 부여 서비스를 뷰에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="7e6db-429">현재 UI에는 사용자가 수정할 수 없는 연락처에 대 한 편집 및 삭제 링크가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="7e6db-430">모든 보기에서 사용할 수 있도록 *views/_ViewImports* 파일에 권한 부여 서비스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="7e6db-431">위의 태그는 여러 문을 추가 합니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="7e6db-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="7e6db-432">적절 한 권한이 있는 사용자에 대해서만 렌더링 되도록 *Pages/Contacts/Index.* s e r v e r/ **삭제** **링크를 업데이트 합니다.**</span><span class="sxs-lookup"><span data-stu-id="7e6db-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="7e6db-433">데이터를 변경할 수 있는 권한이 없는 사용자의 링크를 숨기면 앱이 보호 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="7e6db-434">링크를 숨기면 올바른 링크만 표시 하 여 앱을 보다 사용자에 게 친숙 하 게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="7e6db-435">사용자는 생성 된 Url을 해킹 하 여 자신이 소유 하지 않은 데이터에 대 한 편집 및 삭제 작업을 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="7e6db-436">Razor페이지 또는 컨트롤러는 데이터를 보호 하기 위해 액세스 검사를 적용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="7e6db-437">업데이트 세부 정보</span><span class="sxs-lookup"><span data-stu-id="7e6db-437">Update Details</span></span>

<span data-ttu-id="7e6db-438">관리자가 연락처를 승인 하거나 거부할 수 있도록 세부 정보 보기를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="7e6db-439">세부 정보 페이지 모델을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="7e6db-440">역할에 사용자 추가 또는 제거</span><span class="sxs-lookup"><span data-stu-id="7e6db-440">Add or remove a user to a role</span></span>

<span data-ttu-id="7e6db-441">에 대 한 자세한 내용은 다음 [문제](https://github.com/dotnet/AspNetCore.Docs/issues/8502) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7e6db-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="7e6db-442">사용자가 권한을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-442">Removing privileges from a user.</span></span> <span data-ttu-id="7e6db-443">예를 들어 채팅 앱에서 사용자를 음소거 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="7e6db-444">사용자에 게 권한 추가</span><span class="sxs-lookup"><span data-stu-id="7e6db-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="7e6db-445">완성 된 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="7e6db-445">Test the completed app</span></span>

<span data-ttu-id="7e6db-446">시드 된 사용자 계정에 대 한 암호를 아직 설정 하지 않은 경우 [비밀 관리자 도구](xref:security/app-secrets#secret-manager) 를 사용 하 여 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="7e6db-447">강력한 암호 선택: 8 개 이상의 문자 및 하나 이상의 대문자, 숫자 및 기호를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="7e6db-448">예를 들어 `Passw0rd!` 은 강력한 암호 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="7e6db-449">프로젝트의 폴더에서 다음 명령을 실행 합니다 `<PW>` . 여기서은 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="7e6db-450">데이터베이스 삭제 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="7e6db-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="7e6db-451">응용 프로그램을 다시 시작 하 여 데이터베이스를 시드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="7e6db-452">완성 된 앱을 테스트 하는 쉬운 방법은 세 가지 브라우저 (또는 incognito/InPrivate 세션)를 시작 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="7e6db-453">한 브라우저에서 새 사용자를 등록 합니다 (예: `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="7e6db-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="7e6db-454">다른 사용자로 각 브라우저에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="7e6db-455">다음 작업을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-455">Verify the following operations:</span></span>

* <span data-ttu-id="7e6db-456">등록 된 사용자는 승인 된 모든 연락처 데이터를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="7e6db-457">등록 된 사용자는 자신의 데이터를 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="7e6db-458">관리자는 연락처 데이터를 승인/거부할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="7e6db-459">`Details`보기는 **승인** 및 **거부** 단추를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="7e6db-460">관리자는 모든 데이터를 승인/거부 하 고 편집/삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="7e6db-461">사용자</span><span class="sxs-lookup"><span data-stu-id="7e6db-461">User</span></span>                | <span data-ttu-id="7e6db-462">앱에서 시드</span><span class="sxs-lookup"><span data-stu-id="7e6db-462">Seeded by the app</span></span> | <span data-ttu-id="7e6db-463">옵션</span><span class="sxs-lookup"><span data-stu-id="7e6db-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="7e6db-464">No</span><span class="sxs-lookup"><span data-stu-id="7e6db-464">No</span></span>                | <span data-ttu-id="7e6db-465">자신의 데이터를 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="7e6db-466">Yes</span><span class="sxs-lookup"><span data-stu-id="7e6db-466">Yes</span></span>               | <span data-ttu-id="7e6db-467">자신의 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="7e6db-468">Yes</span><span class="sxs-lookup"><span data-stu-id="7e6db-468">Yes</span></span>               | <span data-ttu-id="7e6db-469">모든 데이터를 승인/거부 하 고 편집/삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="7e6db-470">관리자의 브라우저에서 연락처를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="7e6db-471">관리자 연락처에서 삭제 및 편집에 대 한 URL을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="7e6db-472">이러한 링크를 테스트 사용자의 브라우저에 붙여넣어 테스트 사용자가 이러한 작업을 수행할 수 없는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="7e6db-473">시작 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-473">Create the starter app</span></span>

* <span data-ttu-id="7e6db-474">Razor"연락처 관리자" 라는 페이지 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7e6db-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="7e6db-475">**개별 사용자 계정을**사용 하 여 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="7e6db-476">네임 스페이스는 샘플에서 사용 되는 네임 스페이스와 일치 하도록 "연락처 관리자"로 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="7e6db-477">`-uld`SQLite 대신 LocalDB를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="7e6db-478">*모델/연락처를 추가 합니다. cs*:</span><span class="sxs-lookup"><span data-stu-id="7e6db-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="7e6db-479">모델을 스 캐 폴드 `Contact` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="7e6db-480">초기 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="7e6db-481">*Pages/_Layout. cshtml* 파일에서 지 각 **관리자** 앵커를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="7e6db-482">연락처를 만들고, 편집 하 고, 삭제 하 여 앱을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="7e6db-483">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="7e6db-483">Seed the database</span></span>

<span data-ttu-id="7e6db-484">*데이터* 폴더에 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="7e6db-485">`SeedData.Initialize`다음에서 호출 `Main` :</span><span class="sxs-lookup"><span data-stu-id="7e6db-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="7e6db-486">응용 프로그램이 데이터베이스를 시드 하는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-486">Test that the app seeded the database.</span></span> <span data-ttu-id="7e6db-487">Contact DB에 행이 있는 경우 시드 방법이 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="7e6db-488">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7e6db-488">Additional resources</span></span>

* [<span data-ttu-id="7e6db-489">Azure App Service에서 .NET Core 및 SQL 데이터베이스 웹앱 빌드</span><span class="sxs-lookup"><span data-stu-id="7e6db-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="7e6db-490">[권한 부여 랩을 ASP.NET Core](https://github.com/blowdart/AspNetAuthorizationWorkshop)합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="7e6db-491">이 랩에서는이 자습서에서 소개 하는 보안 기능에 대해 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6db-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="7e6db-492">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7e6db-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
