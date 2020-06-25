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
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292687"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="c49f8-103">ASP.NET Core Blazor 서버 앱 보호</span><span class="sxs-lookup"><span data-stu-id="c49f8-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="c49f8-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="c49f8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="c49f8-105"> 서버 앱은 ASP.NET Core 앱과 동일한 방식으로 보안을 위해 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-105"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="c49f8-106">자세한 내용은 <xref:security/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c49f8-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="c49f8-107">이 개요의 항목은 Blazor 서버에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="c49f8-108"> 서버 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="c49f8-108"> Server project template</span></span>

<span data-ttu-id="c49f8-109">프로젝트를 만들 때 인증을 위해 Blazor 서버 프로젝트 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c49f8-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c49f8-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c49f8-111"><xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c49f8-112">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="c49f8-113">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c49f8-114">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="c49f8-114">**No Authentication**</span></span>
* <span data-ttu-id="c49f8-115">**개별 사용자 계정**: 사용자 계정은 다음과 같이 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="c49f8-116">ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장</span><span class="sxs-lookup"><span data-stu-id="c49f8-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c49f8-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="c49f8-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c49f8-118">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="c49f8-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="c49f8-119">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="c49f8-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c49f8-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c49f8-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c49f8-121"><xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c49f8-122">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c49f8-123">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="c49f8-123">Authentication mechanism</span></span> | <span data-ttu-id="c49f8-124">설명</span><span class="sxs-lookup"><span data-stu-id="c49f8-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c49f8-125">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="c49f8-125">`None` (default)</span></span>         | <span data-ttu-id="c49f8-126">인증 없음</span><span class="sxs-lookup"><span data-stu-id="c49f8-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c49f8-127">ASP.NET Core Identity을 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="c49f8-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c49f8-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="c49f8-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c49f8-129">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c49f8-130">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c49f8-131">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-131">Windows Authentication</span></span> |

<span data-ttu-id="c49f8-132">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c49f8-133">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-133">Create a folder for the project.</span></span>
* <span data-ttu-id="c49f8-134">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-134">Name the project.</span></span>

<span data-ttu-id="c49f8-135">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c49f8-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c49f8-136">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c49f8-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c49f8-137"><xref:blazor/get-started> 문서의 Mac용 Visual Studio 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="c49f8-138">**새 Blazor 서버 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c49f8-139">ASP.NET Core Identity을 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c49f8-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c49f8-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c49f8-141"><xref:blazor/get-started> 문서의 .NET Core CLI 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c49f8-142">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c49f8-143">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="c49f8-143">Authentication mechanism</span></span> | <span data-ttu-id="c49f8-144">설명</span><span class="sxs-lookup"><span data-stu-id="c49f8-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c49f8-145">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="c49f8-145">`None` (default)</span></span>         | <span data-ttu-id="c49f8-146">인증 없음</span><span class="sxs-lookup"><span data-stu-id="c49f8-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c49f8-147">ASP.NET Core Identity을 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="c49f8-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c49f8-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="c49f8-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c49f8-149">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c49f8-150">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c49f8-151">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c49f8-151">Windows Authentication</span></span> |

<span data-ttu-id="c49f8-152">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c49f8-153">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-153">Create a folder for the project.</span></span>
* <span data-ttu-id="c49f8-154">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c49f8-154">Name the project.</span></span>

<span data-ttu-id="c49f8-155">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c49f8-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="c49f8-156">스캐폴드 Identity</span><span class="sxs-lookup"><span data-stu-id="c49f8-156">Scaffold Identity</span></span>

<span data-ttu-id="c49f8-157">Blazor 서버 프로젝트에 스캐폴드 Identity 실행:</span><span class="sxs-lookup"><span data-stu-id="c49f8-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="c49f8-158">[기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="c49f8-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="c49f8-159">[권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="c49f8-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
