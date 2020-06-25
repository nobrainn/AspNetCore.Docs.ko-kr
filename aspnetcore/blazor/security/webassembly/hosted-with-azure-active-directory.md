---
title: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9332eddd3d428e8a25910d387f95b870926d5ae5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103415"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="9e903-102">Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="9e903-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="9e903-103">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9e903-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9e903-104">이 문서에서는 인증을 위해 [AAD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)를 사용하는 [Blazor WebAssembly 호스트형 앱](xref:blazor/hosting-models#blazor-webassembly)을 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="9e903-105">AAD에 앱을 등록하고 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="9e903-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9e903-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="9e903-106">Create a tenant</span></span>

<span data-ttu-id="9e903-107">[빠른 시작: 테넌트 설정](/azure/active-directory/develop/quickstart-create-new-tenant)의 지침에 따라 AAD에서 테넌트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9e903-108">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="9e903-108">Register a server API app</span></span>

<span data-ttu-id="9e903-109">[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 ‘서버 API 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="9e903-110">**Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9e903-111">앱의 **이름**을 지정합니다(예: **Blazor 서버 AAD**).</span><span class="sxs-lookup"><span data-stu-id="9e903-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9e903-112">**지원되는 계정 유형**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="9e903-113">이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9e903-114">이 시나리오에서는 ‘서버 API 앱’에 **리디렉션 URI**가 필요하지 않으므로 드롭다운이 **웹**으로 설정된 상태로 두고 리디렉션 URI를 입력하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9e903-115">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9e903-116">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-116">Select **Register**.</span></span>

<span data-ttu-id="9e903-117">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-117">Record the following information:</span></span>

* <span data-ttu-id="9e903-118">‘서버 API 앱’ 애플리케이션 ID(클라이언트 ID)(예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="9e903-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9e903-119">디렉터리 ID(테넌트 ID)(예: `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="9e903-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="9e903-120">AAD 테넌트 도메인(예: `contoso.onmicrosoft.com`) 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인**으로 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="9e903-121">앱에 로그인 또는 사용자 프로필 액세스가 필요하지 않으므로 **API 사용 권한**에서 **Microsoft Graph** > **User.Read** 사용 권한을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="9e903-122">**API 표시**에서:</span><span class="sxs-lookup"><span data-stu-id="9e903-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="9e903-123">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9e903-124">**저장 및 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9e903-125">**범위 이름**을 지정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="9e903-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9e903-126">**관리자 동의 표시 이름**을 지정합니다(예: `Access API`).</span><span class="sxs-lookup"><span data-stu-id="9e903-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9e903-127">**관리자 동의 설명**을 지정합니다(예: `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="9e903-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9e903-128">**상태**가 **사용**으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9e903-129">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-129">Select **Add scope**.</span></span>

<span data-ttu-id="9e903-130">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-130">Record the following information:</span></span>

* <span data-ttu-id="9e903-131">앱 ID URI(예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 지정한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="9e903-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9e903-132">기본 범위(예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="9e903-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9e903-133">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="9e903-133">Register a client app</span></span>

<span data-ttu-id="9e903-134">[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 ‘클라이언트 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="9e903-135">**Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9e903-136">앱의 **이름**을 지정합니다(예: **Blazor 클라이언트 AAD**).</span><span class="sxs-lookup"><span data-stu-id="9e903-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="9e903-137">**지원되는 계정 유형**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="9e903-138">이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9e903-139">**리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9e903-140">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9e903-141">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9e903-142">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 서버 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9e903-143">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="9e903-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9e903-144">[앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9e903-145">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9e903-146">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-146">Select **Register**.</span></span>

<span data-ttu-id="9e903-147">‘클라이언트 앱’ 애플리케이션 ID(클라이언트 ID)를 기록해 둡니다(예: `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="9e903-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="9e903-148">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="9e903-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9e903-149">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9e903-150">**암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9e903-151">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9e903-152">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-152">Select the **Save** button.</span></span>

<span data-ttu-id="9e903-153">**API 사용 권한**에서:</span><span class="sxs-lookup"><span data-stu-id="9e903-153">In **API permissions**:</span></span>

1. <span data-ttu-id="9e903-154">앱에 **Microsoft Graph** > **User.Read** 사용 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="9e903-155">**사용 권한 추가**를 선택하고 **내 API**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9e903-156">**이름** 열에서 ‘서버 API 앱’을 선택합니다(예: **Blazor 서버 AAD**).</span><span class="sxs-lookup"><span data-stu-id="9e903-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9e903-157">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-157">Open the **API** list.</span></span>
1. <span data-ttu-id="9e903-158">API에 대한 액세스를 사용하도록 설정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="9e903-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9e903-159">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9e903-160">**{테넌트 이름}에 대한 관리자 동의 허용** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="9e903-161">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9e903-162">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="9e903-162">Create the app</span></span>

<span data-ttu-id="9e903-163">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9e903-164">출력 위치를 지정하려면 명령에 경로와 함께 출력 옵션을 포함합니다(예: `-o BlazorSample`). 출력 위치는 프로젝트 폴더가 존재하지 않는 경우 프로젝트 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9e903-165">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9e903-166">앱 ID URI를 `app-id-uri` 옵션으로 전달합니다. 단, 클라이언트 앱에서 구성 변경이 필요할 수 있습니다. 이에 대해서는 [액세스 토큰 범위](#access-token-scopes) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="9e903-167">Azure Portal에서 ‘클라이언트 앱’의 **인증** > **플랫폼 구성** > **웹** > **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9e903-168">‘클라이언트 앱’이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 ‘서버 앱’ 속성에서 확인할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="9e903-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9e903-169">앞에서 ‘클라이언트 앱’의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 ‘클라이언트 앱’의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다. </span><span class="sxs-lookup"><span data-stu-id="9e903-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9e903-170">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9e903-170">Server app configuration</span></span>

<span data-ttu-id="9e903-171">‘이 섹션은 솔루션의 **서버** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="9e903-171">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9e903-172">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="9e903-172">Authentication package</span></span>

<span data-ttu-id="9e903-173">ASP.NET Core Web API에 대한 호출의 권한 부여 및 인증 지원은 [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9e903-174">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="9e903-174">Authentication service support</span></span>

<span data-ttu-id="9e903-175">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9e903-176"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> 메서드는 JWT 전달자 처리기에서 Azure Active Directory에서 내보낸 토큰의 유효성을 검사하는 데 필요한 특정 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="9e903-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="9e903-178">앱이 수신 요청에 대해 토큰의 구문 분석 및 유효성 검사를 시도하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9e903-179">올바른 자격 증명 없이 보호된 리소스에 액세스하려는 요청은 모두 실패하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="9e903-180">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="9e903-180">User.Identity.Name</span></span>

<span data-ttu-id="9e903-181">기본적으로 서버 앱 API는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 유형의 값을 사용하여 `User.Identity.Name`을 채웁니다(예: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="9e903-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="9e903-182">앱이 `name` 클레임 유형으로부터 값을 받도록 구성하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>의 [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-182">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9e903-183">앱 설정</span><span class="sxs-lookup"><span data-stu-id="9e903-183">App settings</span></span>

<span data-ttu-id="9e903-184">*appsettings.json* 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-184">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="9e903-185">예:</span><span class="sxs-lookup"><span data-stu-id="9e903-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="9e903-186">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="9e903-186">WeatherForecast controller</span></span>

<span data-ttu-id="9e903-187">WeatherForecast 컨트롤러(*Controllers/WeatherForecastController.cs*)는 컨트롤러에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 사용하여, 보호된 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="9e903-188">다음과 같은 사항을 이해하는 것이 **중요합니다**.</span><span class="sxs-lookup"><span data-stu-id="9e903-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="9e903-189">이 API 컨트롤러의 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 무단 액세스로부터 이 API 컨트롤러를 보호하는 유일한 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9e903-190">Blazor WebAssembly 앱에서 사용되는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 앱이 올바르게 작동하려면 사용자에게 권한이 부여되어야 한다는 힌트만 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="9e903-191">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9e903-191">Client app configuration</span></span>

<span data-ttu-id="9e903-192">‘이 섹션은 솔루션의 **클라이언트** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="9e903-192">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9e903-193">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="9e903-193">Authentication package</span></span>

<span data-ttu-id="9e903-194">앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))를 위한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="9e903-195">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9e903-196">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9e903-197">[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지는 [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지를 앱에 타동적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-197">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9e903-198">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="9e903-198">Authentication service support</span></span>

<span data-ttu-id="9e903-199">서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스에 대한 지원이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9e903-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e903-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="9e903-201">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="9e903-201">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="9e903-202">사용자 인증에 대한 지원은 [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-202">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="9e903-203">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-203">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9e903-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e903-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9e903-205"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-205">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9e903-206">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-206">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="9e903-207">구성은 *wwwroot/appsettings.json* 파일을 통해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-207">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9e903-208">예:</span><span class="sxs-lookup"><span data-stu-id="9e903-208">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="9e903-209">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="9e903-209">Access token scopes</span></span>

<span data-ttu-id="9e903-210">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-210">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9e903-211">기본적으로 로그인 요청에 포함되어 있음.</span><span class="sxs-lookup"><span data-stu-id="9e903-211">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9e903-212">인증 직후에 액세스 토큰을 프로비저닝하는 데 사용됨.</span><span class="sxs-lookup"><span data-stu-id="9e903-212">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9e903-213">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-213">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9e903-214">필요에 따라 추가 API 앱에 대한 추가 범위가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-214">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9e903-215">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e903-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9e903-216">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="9e903-216">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9e903-217">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="9e903-217">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="9e903-218">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="9e903-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9e903-219">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="9e903-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9e903-220">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e903-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9e903-221">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e903-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9e903-222">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e903-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9e903-223">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e903-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9e903-224">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9e903-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9e903-225">앱 실행</span><span class="sxs-lookup"><span data-stu-id="9e903-225">Run the app</span></span>

<span data-ttu-id="9e903-226">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-226">Run the app from the Server project.</span></span> <span data-ttu-id="9e903-227">Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-227">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9e903-228">도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-228">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9e903-229">**솔루션 탐색기**에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9e903-229">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9e903-230">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9e903-230">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="9e903-231">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="9e903-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9e903-232">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="9e903-232">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
