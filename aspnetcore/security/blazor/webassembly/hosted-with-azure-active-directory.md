---
title: Azure Active Directory를 사용 Blazor 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110930"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="9c9d4-102">Azure Active Directory를 사용 Blazor 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="9c9d4-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="9c9d4-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="9c9d4-104">이 문서의 지침은 ASP.NET Core 3.2 Preview 4에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="9c9d4-105">이 항목은 5 월 24 일 금요일에 Preview 5를 포함 하도록 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="9c9d4-106">이 문서에서는 인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 [ Blazor weasembomhosted 앱](xref:blazor/hosting-models#blazor-webassembly) 을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="9c9d4-107">AAD B2C에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="9c9d4-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9c9d4-108">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="9c9d4-108">Create a tenant</span></span>

<span data-ttu-id="9c9d4-109">[빠른 시작: 테 넌 트를 설정](/azure/active-directory/develop/quickstart-create-new-tenant) 하 여 AAD에서 테 넌 트 만들기의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9c9d4-110">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="9c9d4-110">Register a server API app</span></span>

<span data-ttu-id="9c9d4-111">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *서버 API 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9c9d4-112">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-112">Select **New registration**.</span></span>
1. <span data-ttu-id="9c9d4-113">앱에 대 한 **이름** (예: \*\* Blazor 서버 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9c9d4-114">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="9c9d4-115">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9c9d4-116">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9c9d4-117">**Permissions** > **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9c9d4-118">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-118">Select **Register**.</span></span>

<span data-ttu-id="9c9d4-119">**API 권한**에서 **Microsoft Graph** > **사용자를 제거 합니다.** 앱에 로그인 또는 uer 프로필 액세스가 필요 하지 않으므로 읽기 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="9c9d4-120">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="9c9d4-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="9c9d4-121">**범위 추가를**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9c9d4-122">**저장하고 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9c9d4-123">**범위 이름을** 제공 합니다 (예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="9c9d4-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9c9d4-124">**관리자 동의 표시 이름** (예:)을 `Access API`제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9c9d4-125">**관리자 동의 설명** (예:)을 `Allows the app to access server app API endpoints.`제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9c9d4-126">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9c9d4-127">**범위 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-127">Select **Add scope**.</span></span>

<span data-ttu-id="9c9d4-128">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-128">Record the following information:</span></span>

* <span data-ttu-id="9c9d4-129">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9c9d4-130">앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`또는 사용자가 제공한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9c9d4-131">디렉터리 ID (테 넌 트 ID) (예 `222222222-2222-2222-2222-222222222222`:)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="9c9d4-132">AAD 테 넌 트 도메인 (예 `contoso.onmicrosoft.com`:)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="9c9d4-133">기본 범위 (예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="9c9d4-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9c9d4-134">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="9c9d4-134">Register a client app</span></span>

<span data-ttu-id="9c9d4-135">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD에 응용 프로그램 등록 항목의 지침에 따라 Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 *클라이언트 앱* 에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9c9d4-136">**새 등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-136">Select **New registration**.</span></span>
1. <span data-ttu-id="9c9d4-137">응용 프로그램의 **이름** (예: \*\* Blazor 클라이언트 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="9c9d4-138">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="9c9d4-139">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9c9d4-140">**리디렉션 uri** 드롭다운을 **웹**으로 유지 하 고의 `https://localhost:5001/authentication/login-callback`리디렉션 uri를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="9c9d4-141">**Permissions** > **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9c9d4-142">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-142">Select **Register**.</span></span>

<span data-ttu-id="9c9d4-143">**인증** > **플랫폼 구성** > **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="9c9d4-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9c9d4-144">의 `https://localhost:5001/authentication/login-callback` **리디렉션 URI** 가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9c9d4-145">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9c9d4-146">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9c9d4-147">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-147">Select the **Save** button.</span></span>

<span data-ttu-id="9c9d4-148">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="9c9d4-148">In **API permissions**:</span></span>

1. <span data-ttu-id="9c9d4-149">앱에 사용자 **Microsoft Graph** > 있는지 확인**합니다. 읽기** 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="9c9d4-150">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9c9d4-151">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: \*\* Blazor 서버 AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9c9d4-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9c9d4-152">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-152">Open the **API** list.</span></span>
1. <span data-ttu-id="9c9d4-153">API에 대 한 액세스를 사용 하도록 설정 `API.Access`합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="9c9d4-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9c9d4-154">**권한 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9c9d4-155">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="9c9d4-156">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="9c9d4-157">*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예 `33333333-3333-3333-3333-333333333333`:).</span><span class="sxs-lookup"><span data-stu-id="9c9d4-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9c9d4-158">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="9c9d4-158">Create the app</span></span>

<span data-ttu-id="9c9d4-159">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9c9d4-160">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우 `-o BlazorSample`)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="9c9d4-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9c9d4-161">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9c9d4-162">`app-id-uri` 옵션에 앱 ID URI를 전달 하지만 클라이언트 앱에 구성 변경이 필요할 수도 있습니다 .이에 대 한 자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9c9d4-163">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9c9d4-163">Server app configuration</span></span>

<span data-ttu-id="9c9d4-164">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="9c9d4-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9c9d4-165">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="9c9d4-165">Authentication package</span></span>

<span data-ttu-id="9c9d4-166">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 `Microsoft.AspNetCore.Authentication.AzureAD.UI`지원은에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9c9d4-167">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="9c9d4-167">Authentication service support</span></span>

<span data-ttu-id="9c9d4-168">메서드 `AddAuthentication` 는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9c9d4-169">메서드 `AddAzureADBearer` 는 Azure Active Directory에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="9c9d4-170">`UseAuthentication`그리고 `UseAuthorization` 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="9c9d4-171">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9c9d4-172">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="9c9d4-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="9c9d4-173">User.Identity.Name</span></span>

<span data-ttu-id="9c9d4-174">기본적으로 서버 앱 API는 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 형식 (예: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`)의 값으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="9c9d4-175">클레임 형식에서 값을 받도록 앱을 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 구성 하려면에서 `Startup.ConfigureServices`NameClaimType를 구성 합니다. [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) `name`</span><span class="sxs-lookup"><span data-stu-id="9c9d4-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9c9d4-176">앱 설정</span><span class="sxs-lookup"><span data-stu-id="9c9d4-176">App settings</span></span>

<span data-ttu-id="9c9d4-177">*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="9c9d4-178">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="9c9d4-178">WeatherForecast controller</span></span>

<span data-ttu-id="9c9d4-179">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 되는 `[Authorize]` 특성을 사용 하 여 보호 된 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="9c9d4-180">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="9c9d4-181">이 `[Authorize]` api 컨트롤러의 특성은이 api를 무단 액세스 로부터 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9c9d4-182">Weasemboma 앱에서 사용 되는 특성은 `[Authorize]` 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다. Blazor</span><span class="sxs-lookup"><span data-stu-id="9c9d4-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="9c9d4-183">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9c9d4-183">Client app configuration</span></span>

<span data-ttu-id="9c9d4-184">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="9c9d4-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9c9d4-185">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="9c9d4-185">Authentication package</span></span>

<span data-ttu-id="9c9d4-186">회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="9c9d4-187">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9c9d4-188">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="9c9d4-189">위의 `{VERSION}` 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> 문서에 표시 된 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="9c9d4-190">`Microsoft.Authentication.WebAssembly.Msal` 패키지는 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9c9d4-191">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="9c9d4-191">Authentication service support</span></span>

<span data-ttu-id="9c9d4-192">사용자 인증에 대 한 지원은 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="9c9d4-193">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9c9d4-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9c9d4-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9c9d4-195">메서드 `AddMsalAuthentication` 는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9c9d4-196">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="9c9d4-197">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="9c9d4-197">Access token scopes</span></span>

<span data-ttu-id="9c9d4-198">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9c9d4-199">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9c9d4-200">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9c9d4-201">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9c9d4-202">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="9c9d4-203">Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="9c9d4-204">예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="9c9d4-205">스키마 및 호스트 없이 범위 URI를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="9c9d4-206">자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="9c9d4-207">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="9c9d4-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9c9d4-208">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="9c9d4-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9c9d4-209">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9c9d4-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9c9d4-210">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9c9d4-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9c9d4-211">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9c9d4-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9c9d4-212">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9c9d4-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9c9d4-213">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="9c9d4-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9c9d4-214">앱 실행</span><span class="sxs-lookup"><span data-stu-id="9c9d4-214">Run the app</span></span>

<span data-ttu-id="9c9d4-215">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-215">Run the app from the Server project.</span></span> <span data-ttu-id="9c9d4-216">Visual Studio를 사용 하는 경우 **솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="9c9d4-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9c9d4-217">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="9c9d4-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9c9d4-218">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="9c9d4-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
