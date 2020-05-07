---
title: Azure Active Directory B2C를 사용 Blazor 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b451df023bdea3e76725d5d1301f3c3f44ea5d99
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876207"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="aeea5-102">Azure Active Directory B2C를 사용 Blazor 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="aeea5-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="aeea5-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aeea5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="aeea5-104">이 문서에서는 인증을 위해 Blazor [AAD (Azure Active Directory) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="aeea5-105">AAD B2C에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="aeea5-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="aeea5-106">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="aeea5-106">Create a tenant</span></span>

<span data-ttu-id="aeea5-107">[자습서: Azure Active Directory B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant) 의 지침에 따라 AAD B2C 테 넌 트를 만들고 다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="aeea5-108">AAD B2C 인스턴스 (예: `https://contoso.b2clogin.com/`후행 슬래시를 포함 하는)</span><span class="sxs-lookup"><span data-stu-id="aeea5-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="aeea5-109">AAD B2C 테 넌 트 도메인 (예 `contoso.onmicrosoft.com`:)</span><span class="sxs-lookup"><span data-stu-id="aeea5-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="aeea5-110">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="aeea5-110">Register a server API app</span></span>

<span data-ttu-id="aeea5-111">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="aeea5-112">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-112">Select **New registration**.</span></span>
1. <span data-ttu-id="aeea5-113">앱에 대 한 **이름** (예: \*\* Blazor Server AAD B2C\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="aeea5-114">**지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="aeea5-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="aeea5-115">(다중 테 넌 트)이 환경에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="aeea5-116">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="aeea5-117"> > **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 **되어 있는지 확인**합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="aeea5-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-118">Select **Register**.</span></span>

<span data-ttu-id="aeea5-119">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="aeea5-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="aeea5-120">**범위 추가를**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="aeea5-121">**저장하고 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="aeea5-122">**범위 이름을** 제공 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="aeea5-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="aeea5-123">**관리자 동의 표시 이름** (예:)을 `Access API`제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="aeea5-124">**관리자 동의 설명** (예:)을 `Allows the app to access server app API endpoints.`제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="aeea5-125">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="aeea5-126">**범위 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-126">Select **Add scope**.</span></span>

<span data-ttu-id="aeea5-127">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-127">Record the following information:</span></span>

* <span data-ttu-id="aeea5-128">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="aeea5-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="aeea5-129">앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`또는 사용자가 제공한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="aeea5-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="aeea5-130">디렉터리 ID (테 넌 트 ID) (예 `222222222-2222-2222-2222-222222222222`:)</span><span class="sxs-lookup"><span data-stu-id="aeea5-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="aeea5-131">*서버 API 앱* 앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, AZURE PORTAL 클라이언트 ID에 대 한 값을 기본값으로 사용할 수 있음)</span><span class="sxs-lookup"><span data-stu-id="aeea5-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="aeea5-132">기본 범위 (예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="aeea5-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="aeea5-133">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="aeea5-133">Register a client app</span></span>

<span data-ttu-id="aeea5-134">[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="aeea5-135">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-135">Select **New registration**.</span></span>
1. <span data-ttu-id="aeea5-136">응용 프로그램의 **이름** (예: \*\* Blazor 클라이언트 AAD B2C\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="aeea5-137">**지원 되는 계정 유형에**대해 **모든 조직 디렉터리 또는 모든 Id 공급자의 계정을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**</span><span class="sxs-lookup"><span data-stu-id="aeea5-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="aeea5-138">(다중 테 넌 트)이 환경에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="aeea5-139">**리디렉션 uri** 드롭다운을 **웹**으로 유지 하 고의 `https://localhost:5001/authentication/login-callback`리디렉션 uri를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="aeea5-140"> > **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 **되어 있는지 확인**합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="aeea5-141">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-141">Select **Register**.</span></span>

<span data-ttu-id="aeea5-142">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="aeea5-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="aeea5-143">의 `https://localhost:5001/authentication/login-callback` **리디렉션 URI** 가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="aeea5-144">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="aeea5-145">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="aeea5-146">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-146">Select the **Save** button.</span></span>

<span data-ttu-id="aeea5-147">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="aeea5-147">In **API permissions**:</span></span>

1. <span data-ttu-id="aeea5-148">앱에 사용자 **Microsoft Graph** > 있는지 확인**합니다. 읽기** 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="aeea5-149">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="aeea5-150">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: \*\* Blazor server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="aeea5-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="aeea5-151">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-151">Open the **API** list.</span></span>
1. <span data-ttu-id="aeea5-152">API에 대 한 액세스를 사용 하도록 설정 `API.Access`합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="aeea5-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="aeea5-153">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="aeea5-154">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="aeea5-155">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="aeea5-156">**홈** > **Azure AD B2C**Azure AD B2C > **사용자 흐름**:</span><span class="sxs-lookup"><span data-stu-id="aeea5-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="aeea5-157">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="aeea5-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="aeea5-158">최소한 **응용 프로그램 클레임** > **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="aeea5-159">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-159">Record the following information:</span></span>

* <span data-ttu-id="aeea5-160">*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예 `33333333-3333-3333-3333-333333333333`:).</span><span class="sxs-lookup"><span data-stu-id="aeea5-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="aeea5-161">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="aeea5-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="aeea5-162">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="aeea5-162">Create the app</span></span>

<span data-ttu-id="aeea5-163">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="aeea5-164">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우 `-o BlazorSample`)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="aeea5-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="aeea5-165">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="aeea5-166">`app-id-uri` 옵션에 앱 ID URI를 전달 하지만 클라이언트 앱에 구성 변경이 필요할 수도 있습니다 .이에 대 한 자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="aeea5-167">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="aeea5-167">Server app configuration</span></span>

<span data-ttu-id="aeea5-168">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="aeea5-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aeea5-169">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="aeea5-169">Authentication package</span></span>

<span data-ttu-id="aeea5-170">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`지원은에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="aeea5-171">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="aeea5-171">Authentication service support</span></span>

<span data-ttu-id="aeea5-172">메서드 `AddAuthentication` 는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="aeea5-173">메서드 `AddAzureADB2CBearer` 는 Azure Active Directory B2C에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="aeea5-174">`UseAuthentication`그리고 `UseAuthorization` 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="aeea5-175">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="aeea5-176">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="aeea5-177">정의. Identity. 이름의</span><span class="sxs-lookup"><span data-stu-id="aeea5-177">User.Identity.Name</span></span>

<span data-ttu-id="aeea5-178">기본적으로는 `User.Identity.Name` 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="aeea5-179">클레임 형식에서 값을 받도록 앱을 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 구성 하려면에서 `Startup.ConfigureServices`NameClaimType를 구성 합니다. [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) `name`</span><span class="sxs-lookup"><span data-stu-id="aeea5-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="aeea5-180">앱 설정</span><span class="sxs-lookup"><span data-stu-id="aeea5-180">App settings</span></span>

<span data-ttu-id="aeea5-181">*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="aeea5-182">예:</span><span class="sxs-lookup"><span data-stu-id="aeea5-182">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="aeea5-183">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="aeea5-183">WeatherForecast controller</span></span>

<span data-ttu-id="aeea5-184">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 되는 `[Authorize]` 특성을 사용 하 여 보호 된 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="aeea5-185">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="aeea5-186">이 `[Authorize]` api 컨트롤러의 특성은이 api를 무단 액세스 로부터 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="aeea5-187">Weasemboma 앱에서 사용 되는 특성은 `[Authorize]` 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다. Blazor</span><span class="sxs-lookup"><span data-stu-id="aeea5-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="aeea5-188">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="aeea5-188">Client app configuration</span></span>

<span data-ttu-id="aeea5-189">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="aeea5-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="aeea5-190">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="aeea5-190">Authentication package</span></span>

<span data-ttu-id="aeea5-191">개별 B2C 계정 (`IndividualB2C`)을 사용 하도록 앱을 만들면 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="aeea5-192">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="aeea5-193">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="aeea5-194">위의 `{VERSION}` 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> 문서에 표시 된 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="aeea5-195">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="aeea5-196">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="aeea5-196">Authentication service support</span></span>

<span data-ttu-id="aeea5-197">서버 프로젝트 `HttpClient` 에 대 한 요청을 만들 때 액세스 토큰을 포함 하는 인스턴스에 대 한 지원이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="aeea5-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="aeea5-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="aeea5-199">사용자 인증에 대 한 지원은 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="aeea5-200">이 메서드는 앱이 Identity 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="aeea5-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="aeea5-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="aeea5-202">메서드 `AddMsalAuthentication` 는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="aeea5-203">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="aeea5-204">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="aeea5-205">예:</span><span class="sxs-lookup"><span data-stu-id="aeea5-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="aeea5-206">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="aeea5-206">Access token scopes</span></span>

<span data-ttu-id="aeea5-207">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="aeea5-208">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="aeea5-209">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="aeea5-210">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="aeea5-211">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="aeea5-212">Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="aeea5-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="aeea5-213">예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="aeea5-214">스키마 및 호스트 없이 범위 URI를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="aeea5-215">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="aeea5-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="aeea5-216">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="aeea5-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="aeea5-217">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="aeea5-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="aeea5-218">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="aeea5-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="aeea5-219">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="aeea5-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="aeea5-220">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aeea5-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="aeea5-221">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aeea5-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="aeea5-222">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aeea5-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="aeea5-223">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aeea5-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="aeea5-224">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="aeea5-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="aeea5-225">앱 실행</span><span class="sxs-lookup"><span data-stu-id="aeea5-225">Run the app</span></span>

<span data-ttu-id="aeea5-226">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-226">Run the app from the Server project.</span></span> <span data-ttu-id="aeea5-227">Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeea5-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="aeea5-228">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="aeea5-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="aeea5-229">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="aeea5-229">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="aeea5-230">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="aeea5-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
