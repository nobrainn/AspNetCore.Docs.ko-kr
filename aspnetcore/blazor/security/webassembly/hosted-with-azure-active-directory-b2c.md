---
title: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 호스트형 앱 보호
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 3dfaa043fd2e6bc092c2db828563aeaedaa9d272
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243540"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="13c67-102">Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 호스트형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="13c67-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="13c67-103">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13c67-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="13c67-104">이 문서에서는 인증을 위해 [AAD(Azure Active Directory) B2C](/azure/active-directory-b2c/overview)를 사용하는 Blazor WebAssembly 독립 실행형 앱을 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="13c67-105">AAD B2C에 앱을 등록하고 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="13c67-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="13c67-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="13c67-106">Create a tenant</span></span>

<span data-ttu-id="13c67-107">[자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)의 지침에 따라 AAD B2C 테넌트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="13c67-108">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-108">Record the following information:</span></span>

* <span data-ttu-id="13c67-109">AAD B2C 인스턴스(예: 후행 슬래시를 포함하는 `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="13c67-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="13c67-110">AAD B2C 테넌트 도메인(예: `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="13c67-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="13c67-111">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="13c67-111">Register a server API app</span></span>

<span data-ttu-id="13c67-112">[자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)의 지침에 따라 ‘서버 API 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="13c67-113">**Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="13c67-114">앱의 **이름**을 지정합니다(예: **Blazor 서버 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="13c67-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="13c67-115">**지원되는 계정 유형**으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="13c67-116">이 시나리오에서는 ‘서버 API 앱’에 **리디렉션 URI**가 필요하지 않으므로 드롭다운이 **웹**으로 설정된 상태로 두고 리디렉션 URI를 입력하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="13c67-117">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용**이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="13c67-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-118">Select **Register**.</span></span>

<span data-ttu-id="13c67-119">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-119">Record the following information:</span></span>

* <span data-ttu-id="13c67-120">‘서버 API 앱’ 애플리케이션 ID(클라이언트 ID)(예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="13c67-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="13c67-121">디렉터리 ID(테넌트 ID)(예: `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="13c67-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="13c67-122">AAD 테넌트 도메인(예: `contoso.onmicrosoft.com`) 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인**으로 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="13c67-123">**API 표시**에서:</span><span class="sxs-lookup"><span data-stu-id="13c67-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="13c67-124">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="13c67-125">**저장 및 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="13c67-126">**범위 이름**을 지정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="13c67-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="13c67-127">**관리자 동의 표시 이름**을 지정합니다(예: `Access API`).</span><span class="sxs-lookup"><span data-stu-id="13c67-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="13c67-128">**관리자 동의 설명**을 지정합니다(예: `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="13c67-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="13c67-129">**상태**가 **사용**으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="13c67-130">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-130">Select **Add scope**.</span></span>

<span data-ttu-id="13c67-131">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-131">Record the following information:</span></span>

* <span data-ttu-id="13c67-132">앱 ID URI(예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 지정한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="13c67-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="13c67-133">기본 범위(예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="13c67-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="13c67-134">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="13c67-134">Register a client app</span></span>

<span data-ttu-id="13c67-135">다시 한번 [자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)의 지침에 따라 ‘클라이언트 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="13c67-136">**Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="13c67-137">앱의 **이름**을 지정합니다(예: **Blazor 클라이언트 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="13c67-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="13c67-138">**지원되는 계정 유형**으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="13c67-139">**리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="13c67-140">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="13c67-141">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="13c67-142">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 서버 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="13c67-143">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="13c67-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="13c67-144">[앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="13c67-145">**사용 권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용**이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="13c67-146">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-146">Select **Register**.</span></span>

<span data-ttu-id="13c67-147">애플리케이션 ID(클라이언트 ID)를 기록해 둡니다(예: `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="13c67-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="13c67-148">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="13c67-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="13c67-149">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="13c67-150">**암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="13c67-151">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="13c67-152">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-152">Select the **Save** button.</span></span>

<span data-ttu-id="13c67-153">**API 사용 권한**에서:</span><span class="sxs-lookup"><span data-stu-id="13c67-153">In **API permissions**:</span></span>

1. <span data-ttu-id="13c67-154">**사용 권한 추가**를 선택하고 **내 API**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="13c67-155">**이름** 열에서 ‘서버 API 앱’을 선택합니다(예: **Blazor 서버 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="13c67-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="13c67-156">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-156">Open the **API** list.</span></span>
1. <span data-ttu-id="13c67-157">API에 대한 액세스를 사용하도록 설정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="13c67-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="13c67-158">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="13c67-159">**{테넌트 이름}에 대한 관리자 동의 허용** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="13c67-160">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="13c67-161">**홈** > **Azure AD B2C** > **사용자 흐름**에서:</span><span class="sxs-lookup"><span data-stu-id="13c67-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="13c67-162">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="13c67-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="13c67-163">최소한 **애플리케이션 클레임** > **표시 이름** 사용자 특성을 선택하여 `LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)의 `context.User.Identity.Name`을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="13c67-164">앱에 대해 만들어진 가입 및 로그인 사용자 흐름 이름을 기록해 둡니다(예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="13c67-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="13c67-165">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="13c67-165">Create the app</span></span>

<span data-ttu-id="13c67-166">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="13c67-167">출력 위치를 지정하려면 명령에 경로와 함께 출력 옵션을 포함합니다(예: `-o BlazorSample`). 출력 위치는 프로젝트 폴더가 존재하지 않는 경우 프로젝트 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="13c67-168">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="13c67-169">앱 ID URI를 `app-id-uri` 옵션으로 전달합니다. 단, 클라이언트 앱에서 구성 변경이 필요할 수 있습니다. 이에 대해서는 [액세스 토큰 범위](#access-token-scopes) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="13c67-170">호스팅형 Blazor 템플릿에 의해 설정된 범위에서는 앱 ID URI 호스트가 반복되어 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="13c67-171">‘클라이언트 앱’의 `Program.Main`(`Program.cs`)에서 `DefaultAccessTokenScopes` 컬렉션에 대해 구성된 범위가 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="13c67-172">Azure Portal에서 ‘클라이언트 앱’의 **인증** > **플랫폼 구성** > **웹** > **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="13c67-173">‘클라이언트 앱’이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 ‘서버 앱’ 속성에서 확인할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="13c67-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="13c67-174">앞에서 ‘클라이언트 앱’의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 ‘클라이언트 앱’의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다. </span><span class="sxs-lookup"><span data-stu-id="13c67-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="13c67-175">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="13c67-175">Server app configuration</span></span>

<span data-ttu-id="13c67-176">‘이 섹션은 솔루션의 **`Server`** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="13c67-176">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="13c67-177">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="13c67-177">Authentication package</span></span>

<span data-ttu-id="13c67-178">ASP.NET Core Web API에 대한 호출의 인증 및 권한 부여 지원은 [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="13c67-179">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="13c67-179">Authentication service support</span></span>

<span data-ttu-id="13c67-180">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="13c67-181"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> 메서드는 JWT 전달자 처리기에서 Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사하는 데 필요한 특정 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="13c67-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="13c67-183">앱이 수신 요청에 대해 토큰의 구문 분석 및 유효성 검사를 시도하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="13c67-184">올바른 자격 증명 없이 보호된 리소스에 액세스하려는 요청은 모두 실패하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="13c67-185">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="13c67-185">User.Identity.Name</span></span>

<span data-ttu-id="13c67-186">기본적으로 `User.Identity.Name`은 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="13c67-187">앱이 `name` 클레임 유형으로부터 값을 받도록 구성하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>의 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-187">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="13c67-188">앱 설정</span><span class="sxs-lookup"><span data-stu-id="13c67-188">App settings</span></span>

<span data-ttu-id="13c67-189">`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-189">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="13c67-190">예:</span><span class="sxs-lookup"><span data-stu-id="13c67-190">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="13c67-191">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="13c67-191">WeatherForecast controller</span></span>

<span data-ttu-id="13c67-192">WeatherForecast 컨트롤러(*Controllers/WeatherForecastController.cs*)는 컨트롤러에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 사용하여, 보호된 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="13c67-193">다음과 같은 사항을 이해하는 것이 **중요합니다**.</span><span class="sxs-lookup"><span data-stu-id="13c67-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="13c67-194">이 API 컨트롤러의 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 무단 액세스로부터 이 API 컨트롤러를 보호하는 유일한 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="13c67-195">Blazor WebAssembly 앱에서 사용되는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 앱이 올바르게 작동하려면 사용자에게 권한이 부여되어야 한다는 힌트만 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="13c67-196">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="13c67-196">Client app configuration</span></span>

<span data-ttu-id="13c67-197">‘이 섹션은 솔루션의 **`Client`** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="13c67-197">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="13c67-198">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="13c67-198">Authentication package</span></span>

<span data-ttu-id="13c67-199">앱이 개별 B2C 계정을 사용하도록 만들어진 경우(`IndividualB2C`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="13c67-200">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="13c67-201">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="13c67-202">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-202">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="13c67-203">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="13c67-203">Authentication service support</span></span>

<span data-ttu-id="13c67-204">서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스에 대한 지원이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="13c67-205">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="13c67-205">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="13c67-206">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="13c67-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="13c67-207">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="13c67-208">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="13c67-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="13c67-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="13c67-210"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="13c67-211">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="13c67-212">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-212">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="13c67-213">예:</span><span class="sxs-lookup"><span data-stu-id="13c67-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="13c67-214">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="13c67-214">Access token scopes</span></span>

<span data-ttu-id="13c67-215">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="13c67-216">기본적으로 로그인 요청에 포함되어 있음.</span><span class="sxs-lookup"><span data-stu-id="13c67-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="13c67-217">인증 직후에 액세스 토큰을 프로비저닝하는 데 사용됨.</span><span class="sxs-lookup"><span data-stu-id="13c67-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="13c67-218">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="13c67-219">필요에 따라 추가 API 앱에 대한 추가 범위가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="13c67-220">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="13c67-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="13c67-221">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="13c67-221">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="13c67-222">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="13c67-222">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="13c67-223">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="13c67-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="13c67-224">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="13c67-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="13c67-225">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13c67-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="13c67-226">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13c67-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="13c67-227">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13c67-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="13c67-228">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13c67-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="13c67-229">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="13c67-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="13c67-230">앱 실행</span><span class="sxs-lookup"><span data-stu-id="13c67-230">Run the app</span></span>

<span data-ttu-id="13c67-231">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-231">Run the app from the Server project.</span></span> <span data-ttu-id="13c67-232">Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="13c67-233">도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="13c67-234">**솔루션 탐색기**에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="13c67-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="13c67-235">추가 자료</span><span class="sxs-lookup"><span data-stu-id="13c67-235">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="13c67-236">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="13c67-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="13c67-237">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="13c67-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="13c67-238">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="13c67-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
