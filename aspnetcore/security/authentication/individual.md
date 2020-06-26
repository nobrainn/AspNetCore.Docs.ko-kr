---
title: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서
author: rick-anderson
description: 개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서를 검색 합니다.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406253"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="d53aa-103">개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 프로젝트를 기반으로 하는 문서</span><span class="sxs-lookup"><span data-stu-id="d53aa-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="d53aa-104">ASP.NET Core Identity 는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio의 프로젝트 템플릿에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="d53aa-105">인증 템플릿은 .NET Core CLI에서 사용할 수 있습니다 `-au Individual` .</span><span class="sxs-lookup"><span data-stu-id="d53aa-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="d53aa-106">Web API 인증에 대 한 [이 GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5833) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d53aa-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="d53aa-107">인증 없음</span><span class="sxs-lookup"><span data-stu-id="d53aa-107">No Authentication</span></span>

<span data-ttu-id="d53aa-108">인증은 옵션을 사용 하 여 .NET Core CLI에 지정 됩니다 `-au` .</span><span class="sxs-lookup"><span data-stu-id="d53aa-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="d53aa-109">Visual Studio에서 새 웹 응용 프로그램에 대해 **인증 변경** 대화 상자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="d53aa-110">Visual Studio의 새 웹 앱에 대 한 기본값은 **인증 안 함**입니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="d53aa-111">인증 없이 만든 프로젝트:</span><span class="sxs-lookup"><span data-stu-id="d53aa-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="d53aa-112">로그인 및 로그 아웃 하는 웹 페이지와 UI가 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="d53aa-113">인증 코드를 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="d53aa-114">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-114">Windows Authentication</span></span>

<span data-ttu-id="d53aa-115">.NET Core CLI에서 옵션을 사용 하 여 새 웹 앱에 대 한 Windows 인증을 지정 `-au Windows` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="d53aa-116">Visual Studio에서 **인증 변경** 대화 상자는 **Windows 인증** 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="d53aa-117">Windows 인증을 선택 하면 앱이 [Windows 인증 IIS 모듈](xref:host-and-deploy/iis/modules)을 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="d53aa-118">Windows 인증은 인트라넷 웹 사이트를 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="d53aa-119">dotnet new webapp 인증 옵션</span><span class="sxs-lookup"><span data-stu-id="d53aa-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="d53aa-120">다음 표에서는 새 웹 앱에 사용할 수 있는 인증 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="d53aa-121">옵션</span><span class="sxs-lookup"><span data-stu-id="d53aa-121">Option</span></span> | <span data-ttu-id="d53aa-122">인증 유형</span><span class="sxs-lookup"><span data-stu-id="d53aa-122">Type of authentication</span></span> | <span data-ttu-id="d53aa-123">자세한 정보에 대한 링크</span><span class="sxs-lookup"><span data-stu-id="d53aa-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="d53aa-124">없음</span><span class="sxs-lookup"><span data-stu-id="d53aa-124">None</span></span>            |  <span data-ttu-id="d53aa-125">인증 없음</span><span class="sxs-lookup"><span data-stu-id="d53aa-125">No authentication</span></span> | | 
| <span data-ttu-id="d53aa-126">개인</span><span class="sxs-lookup"><span data-stu-id="d53aa-126">Individual</span></span>      |  <span data-ttu-id="d53aa-127">개별 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="d53aa-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="d53aa-128">IndividualB2C</span></span>   |  <span data-ttu-id="d53aa-129">Azure AD B2C를 사용 하 여 클라우드 호스팅 개별 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="d53aa-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d53aa-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="d53aa-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="d53aa-131">SingleOrg</span></span>       |  <span data-ttu-id="d53aa-132">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="d53aa-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d53aa-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d53aa-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="d53aa-134">MultiOrg</span></span>        |  <span data-ttu-id="d53aa-135">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="d53aa-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d53aa-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d53aa-137">Windows</span><span class="sxs-lookup"><span data-stu-id="d53aa-137">Windows</span></span>         |  <span data-ttu-id="d53aa-138">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-138">Windows authentication</span></span> | [<span data-ttu-id="d53aa-139">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="d53aa-140">Visual Studio new webapp 인증 옵션</span><span class="sxs-lookup"><span data-stu-id="d53aa-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="d53aa-141">다음 표에서는 Visual Studio를 사용 하 여 새 웹 앱을 만들 때 사용할 수 있는 인증 옵션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="d53aa-142">옵션</span><span class="sxs-lookup"><span data-stu-id="d53aa-142">Option</span></span> | <span data-ttu-id="d53aa-143">인증 유형</span><span class="sxs-lookup"><span data-stu-id="d53aa-143">Type of authentication</span></span> | <span data-ttu-id="d53aa-144">자세한 정보에 대한 링크</span><span class="sxs-lookup"><span data-stu-id="d53aa-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="d53aa-145">없음</span><span class="sxs-lookup"><span data-stu-id="d53aa-145">None</span></span>            |  <span data-ttu-id="d53aa-146">인증 없음</span><span class="sxs-lookup"><span data-stu-id="d53aa-146">No authentication</span></span> | | 
| <span data-ttu-id="d53aa-147">개별 사용자 계정/앱 내 사용자 계정 저장</span><span class="sxs-lookup"><span data-stu-id="d53aa-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="d53aa-148">개별 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="d53aa-149">개별 사용자 계정/클라우드의 기존 사용자 저장소에 연결</span><span class="sxs-lookup"><span data-stu-id="d53aa-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="d53aa-150">Azure AD B2C를 사용 하 여 클라우드 호스팅 개별 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="d53aa-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d53aa-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="d53aa-152">회사 또는 학교 클라우드/단일 조직</span><span class="sxs-lookup"><span data-stu-id="d53aa-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="d53aa-153">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="d53aa-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d53aa-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d53aa-155">회사 또는 학교 클라우드/여러 조직</span><span class="sxs-lookup"><span data-stu-id="d53aa-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="d53aa-156">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="d53aa-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="d53aa-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="d53aa-158">Windows</span><span class="sxs-lookup"><span data-stu-id="d53aa-158">Windows</span></span>         |  <span data-ttu-id="d53aa-159">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-159">Windows authentication</span></span> | [<span data-ttu-id="d53aa-160">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="d53aa-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="d53aa-161">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d53aa-161">Additional resources</span></span>

<span data-ttu-id="d53aa-162">다음 문서에서는 개별 사용자 계정을 사용 하는 ASP.NET Core 템플릿에서 생성 된 코드를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53aa-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="d53aa-163">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="d53aa-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d53aa-164">권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d53aa-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
