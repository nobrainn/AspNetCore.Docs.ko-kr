---
title: ASP.NET Core Blazor 서버 앱 보호
author: guardrex
description: Blazor 서버 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103140"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="3ce94-103">ASP.NET Core Blazor 서버 앱 보호</span><span class="sxs-lookup"><span data-stu-id="3ce94-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="3ce94-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="3ce94-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="3ce94-105"> 서버 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="3ce94-105"> Server project template</span></span>

<span data-ttu-id="3ce94-106">프로젝트를 만들 때 인증을 위해 Blazor 서버 프로젝트 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ce94-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ce94-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3ce94-108"><xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="3ce94-109">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="3ce94-110">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="3ce94-111">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="3ce94-111">**No Authentication**</span></span>
* <span data-ttu-id="3ce94-112">**개별 사용자 계정**: 사용자 계정은 다음과 같이 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="3ce94-113">ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장</span><span class="sxs-lookup"><span data-stu-id="3ce94-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="3ce94-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="3ce94-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="3ce94-115">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="3ce94-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="3ce94-116">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="3ce94-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3ce94-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3ce94-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3ce94-118"><xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3ce94-119">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3ce94-120">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="3ce94-120">Authentication mechanism</span></span> | <span data-ttu-id="3ce94-121">설명</span><span class="sxs-lookup"><span data-stu-id="3ce94-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3ce94-122">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="3ce94-122">`None` (default)</span></span>         | <span data-ttu-id="3ce94-123">인증 없음</span><span class="sxs-lookup"><span data-stu-id="3ce94-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3ce94-124">ASP.NET Core Identity을 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="3ce94-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3ce94-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="3ce94-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3ce94-126">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3ce94-127">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3ce94-128">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-128">Windows Authentication</span></span> |

<span data-ttu-id="3ce94-129">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3ce94-130">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-130">Create a folder for the project.</span></span>
* <span data-ttu-id="3ce94-131">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-131">Name the project.</span></span>

<span data-ttu-id="3ce94-132">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3ce94-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3ce94-133">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ce94-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3ce94-134"><xref:blazor/get-started> 문서의 Mac용 Visual Studio 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="3ce94-135">**새 Blazor 서버 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="3ce94-136">ASP.NET Core Identity을 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3ce94-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3ce94-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3ce94-138"><xref:blazor/get-started> 문서의 .NET Core CLI 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3ce94-139">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3ce94-140">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="3ce94-140">Authentication mechanism</span></span> | <span data-ttu-id="3ce94-141">설명</span><span class="sxs-lookup"><span data-stu-id="3ce94-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3ce94-142">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="3ce94-142">`None` (default)</span></span>         | <span data-ttu-id="3ce94-143">인증 없음</span><span class="sxs-lookup"><span data-stu-id="3ce94-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3ce94-144">ASP.NET Core Identity을 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="3ce94-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3ce94-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="3ce94-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3ce94-146">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3ce94-147">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3ce94-148">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="3ce94-148">Windows Authentication</span></span> |

<span data-ttu-id="3ce94-149">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3ce94-150">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-150">Create a folder for the project.</span></span>
* <span data-ttu-id="3ce94-151">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-151">Name the project.</span></span>

<span data-ttu-id="3ce94-152">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3ce94-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="3ce94-153">기존 앱 보호</span><span class="sxs-lookup"><span data-stu-id="3ce94-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="3ce94-154"> 서버 앱은 ASP.NET Core 앱과 동일한 방식으로 보안을 위해 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ce94-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="3ce94-155">자세한 내용은 <xref:security/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3ce94-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="3ce94-156">스캐폴드 Identity</span><span class="sxs-lookup"><span data-stu-id="3ce94-156">Scaffold Identity</span></span>

<span data-ttu-id="3ce94-157">Blazor 서버 프로젝트에 스캐폴드 Identity 실행:</span><span class="sxs-lookup"><span data-stu-id="3ce94-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="3ce94-158">[기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="3ce94-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="3ce94-159">[권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="3ce94-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
