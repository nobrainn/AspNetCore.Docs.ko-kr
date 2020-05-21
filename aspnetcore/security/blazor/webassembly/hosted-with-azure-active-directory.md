---
<span data-ttu-id="73b6e-101">제목: ' Blazor Azure Active Directory ' 만든이를 사용 하 여 ASP.NET Core weasembomomomommbomoms 호스트 된 앱에 보안을 설정 합니다. 설명: 설명: monikerRange: ms. 날짜:</span><span class="sxs-lookup"><span data-stu-id="73b6e-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="73b6e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="73b6e-102">'Blazor'</span></span>
- <span data-ttu-id="73b6e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="73b6e-103">'Identity'</span></span>
- <span data-ttu-id="73b6e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="73b6e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="73b6e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="73b6e-105">'Razor'</span></span>
- <span data-ttu-id="73b6e-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="73b6e-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="73b6e-107">BlazorAzure Active Directory를 사용 하 여 ASP.NET Core weasembomomommbmboman 호스팅된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="73b6e-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="73b6e-108">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="73b6e-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="73b6e-109">이 문서에서는 인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 [ Blazor weasembomhosted 앱](xref:blazor/hosting-models#blazor-webassembly) 을 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="73b6e-110">AAD에 앱 등록 및 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="73b6e-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="73b6e-111">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="73b6e-111">Create a tenant</span></span>

<span data-ttu-id="73b6e-112">[빠른 시작: 테 넌 트를 설정](/azure/active-directory/develop/quickstart-create-new-tenant) 하 여 AAD에서 테 넌 트 만들기의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="73b6e-113">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="73b6e-113">Register a server API app</span></span>

<span data-ttu-id="73b6e-114">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure aad에 응용 프로그램 등록 항목의 지침에 따라 *서버 API 앱*에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="73b6e-115">**Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="73b6e-116">앱에 대 한 **이름** (예: \*\* Blazor 서버 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="73b6e-117">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="73b6e-118">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="73b6e-119">*서버 API 앱* 은이 시나리오에서 **리디렉션 uri** 를 요구 하지 않으므로 드롭다운을 **웹** 으로 설정 하 고 리디렉션 uri를 입력 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="73b6e-120">**Permissions**  >  **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="73b6e-121">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-121">Select **Register**.</span></span>

<span data-ttu-id="73b6e-122">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-122">Record the following information:</span></span>

* <span data-ttu-id="73b6e-123">*서버 API 앱* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="73b6e-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="73b6e-124">디렉터리 ID (테 넌 트 ID) (예: `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="73b6e-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="73b6e-125">AAD 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` ) &ndash; 도메인은 등록 된 앱에 대 한 Azure Portal의 **브랜딩** 블레이드에서 **게시자 도메인** 으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="73b6e-126">**API 권한**에서 앱이 **Microsoft Graph**  >  로그인 또는 사용자 프로필 액세스를 요구 하지 않으므로 사용자 Microsoft Graph을 제거**합니다. 읽기** 권한입니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="73b6e-127">**API 노출**:</span><span class="sxs-lookup"><span data-stu-id="73b6e-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="73b6e-128">**범위 추가를**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="73b6e-129">**저장하고 계속**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="73b6e-130">**범위 이름을** 제공 합니다 (예: `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="73b6e-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="73b6e-131">**관리자 동의 표시 이름** (예:)을 제공 `Access API` 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="73b6e-132">**관리자 동의 설명** (예:)을 제공 `Allows the app to access server app API endpoints.` 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="73b6e-133">**상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="73b6e-134">**범위 추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-134">Select **Add scope**.</span></span>

<span data-ttu-id="73b6e-135">다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-135">Record the following information:</span></span>

* <span data-ttu-id="73b6e-136">앱 ID URI (예: `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` 또는 사용자가 제공한 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="73b6e-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="73b6e-137">기본 범위 (예: `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="73b6e-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="73b6e-138">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="73b6e-138">Register a client app</span></span>

<span data-ttu-id="73b6e-139">[빠른 시작: Microsoft id 플랫폼](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure aad에 응용 프로그램 등록 항목의 지침에 따라 *클라이언트 앱*에 대 한 AAD 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="73b6e-140">**Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="73b6e-141">응용 프로그램의 **이름** (예: \*\* Blazor 클라이언트 AAD\*\*)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="73b6e-142">**지원 되는 계정 유형을**선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="73b6e-143">이 환경 **에서는이 조직 디렉터리에만 계정** (단일 테 넌 트)을 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="73b6e-144">**리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="73b6e-145">Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="73b6e-146">앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="73b6e-147">IIS Express의 경우 **디버그** 패널의 서버 앱 속성에서 앱에 대해 임의로 생성 된 포트를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="73b6e-148">이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="73b6e-149">[응용 프로그램 만들기](#create-the-app) 섹션에는 사용자 IIS EXPRESS 리디렉션 URI를 업데이트 하 라는 알림이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="73b6e-150">**Permissions**  >  **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-150">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="73b6e-151">**등록**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-151">Select **Register**.</span></span>

<span data-ttu-id="73b6e-152">*클라이언트 앱* 응용 프로그램 Id (클라이언트 id)를 기록 합니다 (예: `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="73b6e-152">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="73b6e-153">**인증**  >  **플랫폼 구성**  >  **웹**에서:</span><span class="sxs-lookup"><span data-stu-id="73b6e-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="73b6e-154">의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="73b6e-155">**암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="73b6e-156">앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="73b6e-157">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-157">Select the **Save** button.</span></span>

<span data-ttu-id="73b6e-158">**API 사용 권한**:</span><span class="sxs-lookup"><span data-stu-id="73b6e-158">In **API permissions**:</span></span>

1. <span data-ttu-id="73b6e-159">앱에 사용자 **Microsoft Graph**있는지 확인  >  **합니다. 읽기** 권한이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-159">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="73b6e-160">**권한 추가를** 선택 하 고 **내 api**를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-160">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="73b6e-161">**이름** 열에서 *서버 API 앱* 을 선택 합니다 (예: \*\* Blazor 서버 AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="73b6e-161">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="73b6e-162">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-162">Open the **API** list.</span></span>
1. <span data-ttu-id="73b6e-163">API에 대 한 액세스를 사용 하도록 설정 합니다 (예: `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="73b6e-163">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="73b6e-164">**모든 권한**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-164">Select **Add permissions**.</span></span>
1. <span data-ttu-id="73b6e-165">**{테 넌 트 이름}에 대 한 관리자 콘텐츠 부여** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-165">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="73b6e-166">**예**를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-166">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="73b6e-167">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="73b6e-167">Create the app</span></span>

<span data-ttu-id="73b6e-168">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="73b6e-169">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="73b6e-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="73b6e-170">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="73b6e-171">옵션에 앱 ID URI를 전달 `app-id-uri` 하지만 클라이언트 앱에 구성 변경이 필요할 수도 있습니다 .이에 대 한 자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="73b6e-172">Azure Portal에서 *클라이언트 앱의* **인증**  >  **플랫폼 구성**  >  **웹**  >  **리디렉션 URI** 는 기본 설정으로 kestrel 서버에서 실행 되는 앱에 대해 5001 포트에 대해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="73b6e-173">*클라이언트 앱* 이 임의의 IIS Express 포트에서 실행 되는 경우 응용 프로그램의 포트는 **디버그** 패널의 *서버 앱* 속성에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="73b6e-174">이전에 *클라이언트 앱의* 알려진 포트를 사용 하 여 포트를 구성 하지 않은 경우 Azure Portal의 *클라이언트 앱* 등록으로 돌아가서 올바른 포트를 사용 하 여 리디렉션 URI를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="73b6e-175">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="73b6e-175">Server app configuration</span></span>

<span data-ttu-id="73b6e-176">*이 섹션은 솔루션의 **서버** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="73b6e-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="73b6e-177">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="73b6e-177">Authentication package</span></span>

<span data-ttu-id="73b6e-178">ASP.NET Core 웹 Api에 대 한 호출을 인증 하 고 권한을 부여 하는 지원은에서 제공 됩니다 `Microsoft.AspNetCore.Authentication.AzureAD.UI` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="73b6e-179">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="73b6e-179">Authentication service support</span></span>

<span data-ttu-id="73b6e-180">`AddAuthentication`메서드는 앱 내에서 인증 서비스를 설정 하 고 JWT 전달자 처리기를 기본 인증 방법으로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="73b6e-181">`AddAzureADBearer`메서드는 Azure Active Directory에서 내보낸 토큰의 유효성을 검사 하는 데 필요한 JWT 전달자 처리기의 특정 매개 변수를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-181">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="73b6e-182">`UseAuthentication`그리고 `UseAuthorization` 다음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-182">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="73b6e-183">앱에서 들어오는 요청에 대 한 토큰을 구문 분석 하 고 유효성을 검사 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="73b6e-184">적절 한 자격 증명 없이 보호 된 리소스에 액세스 하려고 시도 하는 모든 요청은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="73b6e-185">사용자. Identity 이름의</span><span class="sxs-lookup"><span data-stu-id="73b6e-185">User.Identity.Name</span></span>

<span data-ttu-id="73b6e-186">기본적으로 서버 앱 API는 `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 형식 (예:)의 값으로 채워집니다 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-186">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="73b6e-187">클레임 형식에서 값을 받도록 앱을 구성 하려면 `name` 에서 [NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) 를 구성 합니다 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="73b6e-188">앱 설정</span><span class="sxs-lookup"><span data-stu-id="73b6e-188">App settings</span></span>

<span data-ttu-id="73b6e-189">*Appsettings* 파일에는 액세스 토큰의 유효성을 검사 하는 데 사용 되는 JWT 전달자 처리기를 구성 하는 옵션이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="73b6e-190">예제:</span><span class="sxs-lookup"><span data-stu-id="73b6e-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="73b6e-191">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="73b6e-191">WeatherForecast controller</span></span>

<span data-ttu-id="73b6e-192">WeatherForecast 컨트롤러 (controller */WeatherForecastController*)는 컨트롤러에 적용 되는 특성을 사용 하 여 보호 된 API를 노출 합니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="73b6e-193">다음을 이해 하는 것이 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="73b6e-194">이 api `[Authorize]` 컨트롤러의 특성은이 api를 무단 액세스 로부터 보호 하는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-194">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="73b6e-195">`[Authorize]`Weasemboma 앱에서 사용 되는 특성은 Blazor 사용자가 앱이 올바르게 작동 하도록 권한이 부여 되어야 한다는 것을 앱에 대 한 힌트로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-195">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="73b6e-196">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="73b6e-196">Client app configuration</span></span>

<span data-ttu-id="73b6e-197">*이 섹션은 솔루션의 **클라이언트** 앱과 관련이 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="73b6e-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="73b6e-198">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="73b6e-198">Authentication package</span></span>

<span data-ttu-id="73b6e-199">회사 또는 학교 계정 ()을 사용 하도록 앱을 만들 때 `SingleOrg` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-199">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="73b6e-200">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="73b6e-201">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="73b6e-202">`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-202">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="73b6e-203">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="73b6e-203">Authentication service support</span></span>

<span data-ttu-id="73b6e-204">`HttpClient`서버 프로젝트에 대 한 요청을 만들 때 액세스 토큰을 포함 하는 인스턴스에 대 한 지원이 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-204">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="73b6e-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="73b6e-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="73b6e-206">사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="73b6e-206">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="73b6e-207">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="73b6e-208">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="73b6e-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="73b6e-209">`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-209">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="73b6e-210">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="73b6e-211">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="73b6e-212">예제:</span><span class="sxs-lookup"><span data-stu-id="73b6e-212">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="73b6e-213">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="73b6e-213">Access token scopes</span></span>

<span data-ttu-id="73b6e-214">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위의 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="73b6e-215">로그인 요청에 기본적으로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="73b6e-216">인증 후 즉시 액세스 토큰을 프로 비전 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="73b6e-217">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="73b6e-218">필요에 따라 추가 API 앱에 대 한 추가 범위를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="73b6e-219">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="73b6e-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="73b6e-220">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="73b6e-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="73b6e-221">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="73b6e-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="73b6e-222">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="73b6e-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="73b6e-223">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="73b6e-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="73b6e-224">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="73b6e-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="73b6e-225">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="73b6e-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="73b6e-226">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="73b6e-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="73b6e-227">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="73b6e-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="73b6e-228">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="73b6e-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="73b6e-229">앱 실행</span><span class="sxs-lookup"><span data-stu-id="73b6e-229">Run the app</span></span>

<span data-ttu-id="73b6e-230">서버 프로젝트에서 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-230">Run the app from the Server project.</span></span> <span data-ttu-id="73b6e-231">Visual Studio를 사용 하는 경우 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="73b6e-232">도구 모음에서 **시작 프로젝트** 드롭다운 목록을 *서버 API 앱* 으로 설정 하 고 **실행** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="73b6e-233">**솔루션 탐색기** 에서 서버 프로젝트를 선택 하 고 도구 모음에서 **실행** 단추를 선택 하거나 **디버그** 메뉴에서 앱을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="73b6e-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="73b6e-234">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="73b6e-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="73b6e-235">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="73b6e-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="73b6e-236">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="73b6e-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
