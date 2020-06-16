---
title: BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: f7b53c8043b53912bcca67bae868e3b516ad2e0d
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776464"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1461c-102">BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="1461c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1461c-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1461c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1461c-104">이 문서에서는 Blazor 인증을 위해 [AAD (AZURE ACTIVE DIRECTORY) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="1461c-105">AAD B2C에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="1461c-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="1461c-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="1461c-106">Create a tenant</span></span>

<span data-ttu-id="1461c-107">[자습서: Azure Active Directory B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant) 의 지침에 따라 AAD B2C 테 넌 트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="1461c-108">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-108">Record the following information:</span></span>

* <span data-ttu-id="1461c-109">AAD B2C 인스턴스 (예: `https://contoso.b2clogin.com/` 후행 슬래시를 포함 하는)</span><span class="sxs-lookup"><span data-stu-id="1461c-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="1461c-110">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="1461c-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="1461c-111">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="1461c-111">Register a server API app</span></span>

<span data-ttu-id="1461c-112">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 *서버 API 앱* 에 대 한 AAD 앱을 등록 하 고 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="1461c-113">**Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="1461c-114">앱에 대 한 **이름** (예: \*\* Blazor Server AAD B2C\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1461c-115">**지원 되는 계정 유형**에 대해 다중 테 넌 트 옵션 ( **모든 조직 디렉터리 또는 모든 Id 공급자의 계정)을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="1461c-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="1461c-116">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="1461c-117">**Permissions**  >  **Openid connect에 게 관리자 동의 부여** 권한이 있는지 확인 하 고 offline_access 권한이 사용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1461c-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-118">Select **Register**.</span></span>

<span data-ttu-id="1461c-119">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-119">Record the following information:</span></span>

* <span data-ttu-id="1461c-120">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="1461c-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1461c-121">디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="1461c-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="1461c-122">AAD 테 넌 트 도메인 (예 `contoso.onmicrosoft.com` :): 등록 된 앱에 대 한 Azure Portal의 **브랜딩** 블레이드에서 **게시자 도메인** 으로 도메인을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="1461c-123">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="1461c-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="1461c-124">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="1461c-125">**저장하고 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="1461c-126">**범위 이름을** 제공 합니다 (예: `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1461c-127">**관리자 동의 표시 이름** (예:)을 제공 `Access API` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="1461c-128">**관리자 동의 설명** (예:)을 제공 `Allows the app to access server app API endpoints.` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="1461c-129">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="1461c-130">**범위 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-130">Select **Add scope**.</span></span>

<span data-ttu-id="1461c-131">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-131">Record the following information:</span></span>

* <span data-ttu-id="1461c-132">앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 제공한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="1461c-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="1461c-133">기본 범위 (예: `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="1461c-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="1461c-134">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="1461c-134">Register a client app</span></span>

<span data-ttu-id="1461c-135">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 *클라이언트 앱* 에 대 한 AAD 앱을 등록 하 고 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="1461c-136">**Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="1461c-137">응용 프로그램의 **이름** (예: \*\* Blazor 클라이언트 AAD B2C\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="1461c-138">**지원 되는 계정 유형**에 대해 다중 테 넌 트 옵션 ( **모든 조직 디렉터리 또는 모든 Id 공급자의 계정)을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="1461c-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="1461c-139">**리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="1461c-140">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="1461c-141">앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="1461c-142">IIS Express의 경우 **디버그** 패널의 서버 앱 속성에서 앱에 대해 임의로 생성 된 포트를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="1461c-143">이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="1461c-144">[응용 프로그램 만들기](#create-the-app) 섹션에는 사용자 IIS EXPRESS 리디렉션 URI를 업데이트 하 라는 알림이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="1461c-145">**Permissions**  >  **Openid connect에 게 관리자 동의 부여** 권한이 있는지 확인 하 고 offline_access 권한이 사용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1461c-146">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-146">Select **Register**.</span></span>

<span data-ttu-id="1461c-147">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="1461c-148">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="1461c-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1461c-149">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1461c-150">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1461c-151">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1461c-152">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-152">Select the **Save** button.</span></span>

<span data-ttu-id="1461c-153">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="1461c-153">In **API permissions**:</span></span>

1. <span data-ttu-id="1461c-154">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="1461c-155">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: \*\* Blazor server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="1461c-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1461c-156">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-156">Open the **API** list.</span></span>
1. <span data-ttu-id="1461c-157">API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1461c-158">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="1461c-159">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="1461c-160">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="1461c-161">**홈**  >  **Azure AD B2C**  >  **사용자 흐름**:</span><span class="sxs-lookup"><span data-stu-id="1461c-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="1461c-162">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="1461c-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="1461c-163">최소한 **응용 프로그램 클레임**  >  **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="1461c-164">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="1461c-165">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="1461c-165">Create the app</span></span>

<span data-ttu-id="1461c-166">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1461c-167">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1461c-168">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="1461c-169">옵션에 앱 ID URI를 전달 `app-id-uri` 하지만 클라이언트 앱에 구성 변경이 필요할 수도 있습니다 .이에 대 한 자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="1461c-170">또한 호스트 된 템플릿에 의해 설정 된 범위에 Blazor 앱 ID URI 호스트가 반복 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="1461c-171">컬렉션에 대해 구성 된 범위가 `DefaultAccessTokenScopes` `Program.Main` *클라이언트 앱*의 (*Program.cs*)에서 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="1461c-172">Azure Portal에서 *클라이언트 앱의* **인증**  >  **플랫폼 구성**  >  **웹**  >  **리디렉션 URI** 는 기본 설정으로 kestrel 서버에서 실행 되는 앱에 대해 5001 포트에 대해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="1461c-173">*클라이언트 앱* 이 임의의 IIS Express 포트에서 실행 되는 경우 응용 프로그램의 포트는 **디버그** 패널의 *서버 앱* 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="1461c-174">이전에 *클라이언트 앱의* 알려진 포트를 사용 하 여 포트를 구성 하지 않은 경우 Azure Portal의 *클라이언트 앱* 등록으로 돌아가서 올바른 포트를 사용 하 여 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="1461c-175">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="1461c-175">Server app configuration</span></span>

<span data-ttu-id="1461c-176">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="1461c-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1461c-177">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="1461c-177">Authentication package</span></span>

<span data-ttu-id="1461c-178">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 지원은 [AzureADB2C](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) 패키지에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="1461c-179">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="1461c-179">Authentication service support</span></span>

<span data-ttu-id="1461c-180">`AddAuthentication`메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="1461c-181"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>메서드는 Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="1461c-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>그리고 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="1461c-183">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="1461c-184">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="1461c-185">사용자. Identity 이름의</span><span class="sxs-lookup"><span data-stu-id="1461c-185">User.Identity.Name</span></span>

<span data-ttu-id="1461c-186">기본적으로는 `User.Identity.Name` 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="1461c-187">클레임 형식에서 값을 받도록 앱을 구성 하려면 `name` 에서 [NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) 를 구성 합니다 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1461c-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="1461c-188">앱 설정</span><span class="sxs-lookup"><span data-stu-id="1461c-188">App settings</span></span>

<span data-ttu-id="1461c-189">파일 *의appsettings.js* 에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="1461c-190">예:</span><span class="sxs-lookup"><span data-stu-id="1461c-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="1461c-191">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="1461c-191">WeatherForecast controller</span></span>

<span data-ttu-id="1461c-192">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 되는 특성을 사용 하 여 보호 된 API를 노출 합니다 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="1461c-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="1461c-193">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="1461c-194">이 api [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 컨트롤러의 특성은이 api를 무단 액세스 로부터 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="1461c-195">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Weasemboma 앱에서 사용 되는 특성은 Blazor 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="1461c-196">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="1461c-196">Client app configuration</span></span>

<span data-ttu-id="1461c-197">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="1461c-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1461c-198">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="1461c-198">Authentication package</span></span>

<span data-ttu-id="1461c-199">개별 B2C 계정 ()을 사용 하도록 앱을 만들면 `IndividualB2C` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)받습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="1461c-200">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1461c-201">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="1461c-202">AspNetCore [패키지는 응용](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 프로그램에 비 전이적으로 인증 패키지를 추가 하 여 전이적으로 추가 합니다 ( [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ).</span><span class="sxs-lookup"><span data-stu-id="1461c-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="1461c-203">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="1461c-203">Authentication service support</span></span>

<span data-ttu-id="1461c-204"><xref:System.Net.Http.HttpClient>서버 프로젝트에 대 한 요청을 만들 때 액세스 토큰을 포함 하는 인스턴스에 대 한 지원이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="1461c-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1461c-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="1461c-206">자리 표시자는 `{APP ASSEMBLY}` 응용 프로그램의 어셈블리 이름입니다 (예: `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="1461c-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="1461c-207">사용자 인증 지원은 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="1461c-208">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1461c-209">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1461c-209">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="1461c-210"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1461c-211">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="1461c-212">구성은 파일 *의 wwwroot/appsettings.js* 에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-212">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="1461c-213">예:</span><span class="sxs-lookup"><span data-stu-id="1461c-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="1461c-214">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="1461c-214">Access token scopes</span></span>

<span data-ttu-id="1461c-215">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="1461c-216">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="1461c-217">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="1461c-218">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="1461c-219">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="1461c-220">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="1461c-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1461c-221">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="1461c-221">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1461c-222">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="1461c-222">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="1461c-223">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="1461c-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="1461c-224">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="1461c-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="1461c-225">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1461c-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="1461c-226">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1461c-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="1461c-227">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1461c-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="1461c-228">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1461c-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="1461c-229">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="1461c-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="1461c-230">앱 실행</span><span class="sxs-lookup"><span data-stu-id="1461c-230">Run the app</span></span>

<span data-ttu-id="1461c-231">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-231">Run the app from the Server project.</span></span> <span data-ttu-id="1461c-232">Visual Studio를 사용 하는 경우 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="1461c-233">도구 모음에서 **시작 프로젝트** 드롭다운 목록을 *서버 API 앱* 으로 설정 하 고 **실행** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="1461c-234">**솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="1461c-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1461c-235">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="1461c-235">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1461c-236">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="1461c-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1461c-237">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="1461c-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1461c-238">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="1461c-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
