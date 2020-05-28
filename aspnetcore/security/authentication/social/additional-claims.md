---
<span data-ttu-id="856bc-101">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-102">'Blazor'</span></span>
- <span data-ttu-id="856bc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-103">'Identity'</span></span>
- <span data-ttu-id="856bc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-105">'Razor'</span></span>
- <span data-ttu-id="856bc-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="856bc-107">ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지</span><span class="sxs-lookup"><span data-stu-id="856bc-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="856bc-108">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="856bc-109">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="856bc-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="856bc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="856bc-111">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="856bc-111">Prerequisites</span></span>

<span data-ttu-id="856bc-112">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="856bc-113">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="856bc-114">자세한 내용은 <xref:security/authentication/social/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="856bc-115">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="856bc-116">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-116">Set the client ID and client secret</span></span>

<span data-ttu-id="856bc-117">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="856bc-118">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="856bc-119">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="856bc-120">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="856bc-121">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="856bc-122">Google 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="856bc-123">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="856bc-124">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="856bc-125">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="856bc-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="856bc-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="856bc-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="856bc-127">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="856bc-128">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="856bc-128">Establish the authentication scope</span></span>

<span data-ttu-id="856bc-129">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="856bc-130">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="856bc-131">공급자</span><span class="sxs-lookup"><span data-stu-id="856bc-131">Provider</span></span>  | <span data-ttu-id="856bc-132">범위</span><span class="sxs-lookup"><span data-stu-id="856bc-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="856bc-133">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-134">'Blazor'</span></span>
- <span data-ttu-id="856bc-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-135">'Identity'</span></span>
- <span data-ttu-id="856bc-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-137">'Razor'</span></span>
- <span data-ttu-id="856bc-138">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-139">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-140">'Blazor'</span></span>
- <span data-ttu-id="856bc-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-141">'Identity'</span></span>
- <span data-ttu-id="856bc-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-143">'Razor'</span></span>
- <span data-ttu-id="856bc-144">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-144">'SignalR' uid:</span></span> 

<span data-ttu-id="856bc-145">----- | ---제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-146">'Blazor'</span></span>
- <span data-ttu-id="856bc-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-147">'Identity'</span></span>
- <span data-ttu-id="856bc-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-149">'Razor'</span></span>
- <span data-ttu-id="856bc-150">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-151">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-152">'Blazor'</span></span>
- <span data-ttu-id="856bc-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-153">'Identity'</span></span>
- <span data-ttu-id="856bc-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-155">'Razor'</span></span>
- <span data-ttu-id="856bc-156">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-157">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-158">'Blazor'</span></span>
- <span data-ttu-id="856bc-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-159">'Identity'</span></span>
- <span data-ttu-id="856bc-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-161">'Razor'</span></span>
- <span data-ttu-id="856bc-162">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-163">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-164">'Blazor'</span></span>
- <span data-ttu-id="856bc-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-165">'Identity'</span></span>
- <span data-ttu-id="856bc-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-167">'Razor'</span></span>
- <span data-ttu-id="856bc-168">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-169">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-170">'Blazor'</span></span>
- <span data-ttu-id="856bc-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-171">'Identity'</span></span>
- <span data-ttu-id="856bc-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-173">'Razor'</span></span>
- <span data-ttu-id="856bc-174">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-175">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-176">'Blazor'</span></span>
- <span data-ttu-id="856bc-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-177">'Identity'</span></span>
- <span data-ttu-id="856bc-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-179">'Razor'</span></span>
- <span data-ttu-id="856bc-180">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-181">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-182">'Blazor'</span></span>
- <span data-ttu-id="856bc-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-183">'Identity'</span></span>
- <span data-ttu-id="856bc-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-185">'Razor'</span></span>
- <span data-ttu-id="856bc-186">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-187">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-188">'Blazor'</span></span>
- <span data-ttu-id="856bc-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-189">'Identity'</span></span>
- <span data-ttu-id="856bc-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-191">'Razor'</span></span>
- <span data-ttu-id="856bc-192">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-193">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-194">'Blazor'</span></span>
- <span data-ttu-id="856bc-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-195">'Identity'</span></span>
- <span data-ttu-id="856bc-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-197">'Razor'</span></span>
- <span data-ttu-id="856bc-198">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-199">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-200">'Blazor'</span></span>
- <span data-ttu-id="856bc-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-201">'Identity'</span></span>
- <span data-ttu-id="856bc-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-203">'Razor'</span></span>
- <span data-ttu-id="856bc-204">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-205">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-206">'Blazor'</span></span>
- <span data-ttu-id="856bc-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-207">'Identity'</span></span>
- <span data-ttu-id="856bc-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-209">'Razor'</span></span>
- <span data-ttu-id="856bc-210">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-211">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-212">'Blazor'</span></span>
- <span data-ttu-id="856bc-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-213">'Identity'</span></span>
- <span data-ttu-id="856bc-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-215">'Razor'</span></span>
- <span data-ttu-id="856bc-216">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-217">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-218">'Blazor'</span></span>
- <span data-ttu-id="856bc-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-219">'Identity'</span></span>
- <span data-ttu-id="856bc-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-221">'Razor'</span></span>
- <span data-ttu-id="856bc-222">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-223">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-224">'Blazor'</span></span>
- <span data-ttu-id="856bc-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-225">'Identity'</span></span>
- <span data-ttu-id="856bc-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-227">'Razor'</span></span>
- <span data-ttu-id="856bc-228">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-229">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-230">'Blazor'</span></span>
- <span data-ttu-id="856bc-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-231">'Identity'</span></span>
- <span data-ttu-id="856bc-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-233">'Razor'</span></span>
- <span data-ttu-id="856bc-234">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-235">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-236">'Blazor'</span></span>
- <span data-ttu-id="856bc-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-237">'Identity'</span></span>
- <span data-ttu-id="856bc-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-239">'Razor'</span></span>
- <span data-ttu-id="856bc-240">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-241">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-242">'Blazor'</span></span>
- <span data-ttu-id="856bc-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-243">'Identity'</span></span>
- <span data-ttu-id="856bc-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-245">'Razor'</span></span>
- <span data-ttu-id="856bc-246">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-247">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-248">'Blazor'</span></span>
- <span data-ttu-id="856bc-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-249">'Identity'</span></span>
- <span data-ttu-id="856bc-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-251">'Razor'</span></span>
- <span data-ttu-id="856bc-252">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-253">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-254">'Blazor'</span></span>
- <span data-ttu-id="856bc-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-255">'Identity'</span></span>
- <span data-ttu-id="856bc-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-257">'Razor'</span></span>
- <span data-ttu-id="856bc-258">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-259">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-260">'Blazor'</span></span>
- <span data-ttu-id="856bc-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-261">'Identity'</span></span>
- <span data-ttu-id="856bc-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-263">'Razor'</span></span>
- <span data-ttu-id="856bc-264">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-265">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-266">'Blazor'</span></span>
- <span data-ttu-id="856bc-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-267">'Identity'</span></span>
- <span data-ttu-id="856bc-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-269">'Razor'</span></span>
- <span data-ttu-id="856bc-270">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-271">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-272">'Blazor'</span></span>
- <span data-ttu-id="856bc-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-273">'Identity'</span></span>
- <span data-ttu-id="856bc-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-275">'Razor'</span></span>
- <span data-ttu-id="856bc-276">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-277">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-278">'Blazor'</span></span>
- <span data-ttu-id="856bc-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-279">'Identity'</span></span>
- <span data-ttu-id="856bc-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-281">'Razor'</span></span>
- <span data-ttu-id="856bc-282">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-283">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-284">'Blazor'</span></span>
- <span data-ttu-id="856bc-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-285">'Identity'</span></span>
- <span data-ttu-id="856bc-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-287">'Razor'</span></span>
- <span data-ttu-id="856bc-288">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-289">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-290">'Blazor'</span></span>
- <span data-ttu-id="856bc-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-291">'Identity'</span></span>
- <span data-ttu-id="856bc-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-293">'Razor'</span></span>
- <span data-ttu-id="856bc-294">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-295">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-296">'Blazor'</span></span>
- <span data-ttu-id="856bc-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-297">'Identity'</span></span>
- <span data-ttu-id="856bc-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-299">'Razor'</span></span>
- <span data-ttu-id="856bc-300">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-301">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-302">'Blazor'</span></span>
- <span data-ttu-id="856bc-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-303">'Identity'</span></span>
- <span data-ttu-id="856bc-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-305">'Razor'</span></span>
- <span data-ttu-id="856bc-306">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-307">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-308">'Blazor'</span></span>
- <span data-ttu-id="856bc-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-309">'Identity'</span></span>
- <span data-ttu-id="856bc-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-311">'Razor'</span></span>
- <span data-ttu-id="856bc-312">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-313">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-314">'Blazor'</span></span>
- <span data-ttu-id="856bc-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-315">'Identity'</span></span>
- <span data-ttu-id="856bc-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-317">'Razor'</span></span>
- <span data-ttu-id="856bc-318">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-319">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-320">'Blazor'</span></span>
- <span data-ttu-id="856bc-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-321">'Identity'</span></span>
- <span data-ttu-id="856bc-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-323">'Razor'</span></span>
- <span data-ttu-id="856bc-324">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-324">'SignalR' uid:</span></span> 

<span data-ttu-id="856bc-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="856bc-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="856bc-326">샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="856bc-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="856bc-327">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="856bc-328">다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="856bc-329">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="856bc-329">Map user data keys and create claims</span></span>

<span data-ttu-id="856bc-330">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="856bc-331">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="856bc-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="856bc-332">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="856bc-333">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="856bc-334">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="856bc-335">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="856bc-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="856bc-336">기본적으로 사용자의 클레임은 인증 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="856bc-337">인증 쿠키가 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="856bc-338">브라우저에서 쿠키 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="856bc-339">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="856bc-340">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="856bc-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="856bc-341">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="856bc-342">쿠키 인증 미들웨어의 사용자 지정을 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 하 여 요청 간에 id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="856bc-343">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="856bc-344">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="856bc-344">Save the access token</span></span>

<span data-ttu-id="856bc-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="856bc-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="856bc-346">`SaveTokens`는 `false` 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="856bc-347">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="856bc-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="856bc-348">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="856bc-349">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d*s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="856bc-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="856bc-350">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="856bc-350">How to add additional custom tokens</span></span>

<span data-ttu-id="856bc-351">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="856bc-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="856bc-352">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="856bc-352">Creating and adding claims</span></span>

<span data-ttu-id="856bc-353">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="856bc-354">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="856bc-355">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="856bc-356">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="856bc-357">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="856bc-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="856bc-358">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="856bc-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="856bc-359">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="856bc-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="856bc-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="856bc-361">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="856bc-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="856bc-362">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="856bc-363">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="856bc-364">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="856bc-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="856bc-365">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="856bc-365">Prerequisites</span></span>

<span data-ttu-id="856bc-366">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="856bc-367">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="856bc-368">자세한 내용은 <xref:security/authentication/social/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="856bc-369">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="856bc-370">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-370">Set the client ID and client secret</span></span>

<span data-ttu-id="856bc-371">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="856bc-372">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="856bc-373">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="856bc-374">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="856bc-375">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="856bc-376">Google 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="856bc-377">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="856bc-378">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="856bc-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="856bc-379">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="856bc-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="856bc-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="856bc-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="856bc-381">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="856bc-382">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="856bc-382">Establish the authentication scope</span></span>

<span data-ttu-id="856bc-383">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="856bc-384">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="856bc-385">공급자</span><span class="sxs-lookup"><span data-stu-id="856bc-385">Provider</span></span>  | <span data-ttu-id="856bc-386">범위</span><span class="sxs-lookup"><span data-stu-id="856bc-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="856bc-387">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-388">'Blazor'</span></span>
- <span data-ttu-id="856bc-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-389">'Identity'</span></span>
- <span data-ttu-id="856bc-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-391">'Razor'</span></span>
- <span data-ttu-id="856bc-392">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-393">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-394">'Blazor'</span></span>
- <span data-ttu-id="856bc-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-395">'Identity'</span></span>
- <span data-ttu-id="856bc-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-397">'Razor'</span></span>
- <span data-ttu-id="856bc-398">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-398">'SignalR' uid:</span></span> 

<span data-ttu-id="856bc-399">----- | ---제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-400">'Blazor'</span></span>
- <span data-ttu-id="856bc-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-401">'Identity'</span></span>
- <span data-ttu-id="856bc-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-403">'Razor'</span></span>
- <span data-ttu-id="856bc-404">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-405">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-406">'Blazor'</span></span>
- <span data-ttu-id="856bc-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-407">'Identity'</span></span>
- <span data-ttu-id="856bc-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-409">'Razor'</span></span>
- <span data-ttu-id="856bc-410">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-411">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-412">'Blazor'</span></span>
- <span data-ttu-id="856bc-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-413">'Identity'</span></span>
- <span data-ttu-id="856bc-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-415">'Razor'</span></span>
- <span data-ttu-id="856bc-416">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-417">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-418">'Blazor'</span></span>
- <span data-ttu-id="856bc-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-419">'Identity'</span></span>
- <span data-ttu-id="856bc-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-421">'Razor'</span></span>
- <span data-ttu-id="856bc-422">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-423">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-424">'Blazor'</span></span>
- <span data-ttu-id="856bc-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-425">'Identity'</span></span>
- <span data-ttu-id="856bc-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-427">'Razor'</span></span>
- <span data-ttu-id="856bc-428">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-429">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-430">'Blazor'</span></span>
- <span data-ttu-id="856bc-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-431">'Identity'</span></span>
- <span data-ttu-id="856bc-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-433">'Razor'</span></span>
- <span data-ttu-id="856bc-434">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-435">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-436">'Blazor'</span></span>
- <span data-ttu-id="856bc-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-437">'Identity'</span></span>
- <span data-ttu-id="856bc-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-439">'Razor'</span></span>
- <span data-ttu-id="856bc-440">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-441">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-442">'Blazor'</span></span>
- <span data-ttu-id="856bc-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-443">'Identity'</span></span>
- <span data-ttu-id="856bc-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-445">'Razor'</span></span>
- <span data-ttu-id="856bc-446">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-447">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-448">'Blazor'</span></span>
- <span data-ttu-id="856bc-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-449">'Identity'</span></span>
- <span data-ttu-id="856bc-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-451">'Razor'</span></span>
- <span data-ttu-id="856bc-452">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-453">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-454">'Blazor'</span></span>
- <span data-ttu-id="856bc-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-455">'Identity'</span></span>
- <span data-ttu-id="856bc-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-457">'Razor'</span></span>
- <span data-ttu-id="856bc-458">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-459">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-460">'Blazor'</span></span>
- <span data-ttu-id="856bc-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-461">'Identity'</span></span>
- <span data-ttu-id="856bc-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-463">'Razor'</span></span>
- <span data-ttu-id="856bc-464">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-465">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-466">'Blazor'</span></span>
- <span data-ttu-id="856bc-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-467">'Identity'</span></span>
- <span data-ttu-id="856bc-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-469">'Razor'</span></span>
- <span data-ttu-id="856bc-470">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-471">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-472">'Blazor'</span></span>
- <span data-ttu-id="856bc-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-473">'Identity'</span></span>
- <span data-ttu-id="856bc-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-475">'Razor'</span></span>
- <span data-ttu-id="856bc-476">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-477">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-478">'Blazor'</span></span>
- <span data-ttu-id="856bc-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-479">'Identity'</span></span>
- <span data-ttu-id="856bc-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-481">'Razor'</span></span>
- <span data-ttu-id="856bc-482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-483">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-484">'Blazor'</span></span>
- <span data-ttu-id="856bc-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-485">'Identity'</span></span>
- <span data-ttu-id="856bc-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-487">'Razor'</span></span>
- <span data-ttu-id="856bc-488">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-489">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-490">'Blazor'</span></span>
- <span data-ttu-id="856bc-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-491">'Identity'</span></span>
- <span data-ttu-id="856bc-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-493">'Razor'</span></span>
- <span data-ttu-id="856bc-494">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-495">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-496">'Blazor'</span></span>
- <span data-ttu-id="856bc-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-497">'Identity'</span></span>
- <span data-ttu-id="856bc-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-499">'Razor'</span></span>
- <span data-ttu-id="856bc-500">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-501">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-502">'Blazor'</span></span>
- <span data-ttu-id="856bc-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-503">'Identity'</span></span>
- <span data-ttu-id="856bc-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-505">'Razor'</span></span>
- <span data-ttu-id="856bc-506">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-507">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-508">'Blazor'</span></span>
- <span data-ttu-id="856bc-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-509">'Identity'</span></span>
- <span data-ttu-id="856bc-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-511">'Razor'</span></span>
- <span data-ttu-id="856bc-512">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-513">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-514">'Blazor'</span></span>
- <span data-ttu-id="856bc-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-515">'Identity'</span></span>
- <span data-ttu-id="856bc-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-517">'Razor'</span></span>
- <span data-ttu-id="856bc-518">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-519">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-520">'Blazor'</span></span>
- <span data-ttu-id="856bc-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-521">'Identity'</span></span>
- <span data-ttu-id="856bc-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-523">'Razor'</span></span>
- <span data-ttu-id="856bc-524">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-525">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-526">'Blazor'</span></span>
- <span data-ttu-id="856bc-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-527">'Identity'</span></span>
- <span data-ttu-id="856bc-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-529">'Razor'</span></span>
- <span data-ttu-id="856bc-530">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-531">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-532">'Blazor'</span></span>
- <span data-ttu-id="856bc-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-533">'Identity'</span></span>
- <span data-ttu-id="856bc-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-535">'Razor'</span></span>
- <span data-ttu-id="856bc-536">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-537">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-538">'Blazor'</span></span>
- <span data-ttu-id="856bc-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-539">'Identity'</span></span>
- <span data-ttu-id="856bc-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-541">'Razor'</span></span>
- <span data-ttu-id="856bc-542">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-543">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-544">'Blazor'</span></span>
- <span data-ttu-id="856bc-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-545">'Identity'</span></span>
- <span data-ttu-id="856bc-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-547">'Razor'</span></span>
- <span data-ttu-id="856bc-548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-549">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-550">'Blazor'</span></span>
- <span data-ttu-id="856bc-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-551">'Identity'</span></span>
- <span data-ttu-id="856bc-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-553">'Razor'</span></span>
- <span data-ttu-id="856bc-554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-555">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-556">'Blazor'</span></span>
- <span data-ttu-id="856bc-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-557">'Identity'</span></span>
- <span data-ttu-id="856bc-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-559">'Razor'</span></span>
- <span data-ttu-id="856bc-560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-561">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-562">'Blazor'</span></span>
- <span data-ttu-id="856bc-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-563">'Identity'</span></span>
- <span data-ttu-id="856bc-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-565">'Razor'</span></span>
- <span data-ttu-id="856bc-566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-567">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-568">'Blazor'</span></span>
- <span data-ttu-id="856bc-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-569">'Identity'</span></span>
- <span data-ttu-id="856bc-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-571">'Razor'</span></span>
- <span data-ttu-id="856bc-572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="856bc-573">제목: author: 설명: monikerRange: ms: custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="856bc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="856bc-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="856bc-574">'Blazor'</span></span>
- <span data-ttu-id="856bc-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="856bc-575">'Identity'</span></span>
- <span data-ttu-id="856bc-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="856bc-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="856bc-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="856bc-577">'Razor'</span></span>
- <span data-ttu-id="856bc-578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="856bc-578">'SignalR' uid:</span></span> 

<span data-ttu-id="856bc-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="856bc-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="856bc-580">샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="856bc-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="856bc-581">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="856bc-582">다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="856bc-583">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="856bc-583">Map user data keys and create claims</span></span>

<span data-ttu-id="856bc-584">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="856bc-585">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="856bc-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="856bc-586">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="856bc-587">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="856bc-588">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="856bc-589">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="856bc-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="856bc-590">기본적으로 사용자의 클레임은 인증 쿠키에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="856bc-591">인증 쿠키가 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="856bc-592">브라우저에서 쿠키 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="856bc-593">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="856bc-594">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="856bc-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="856bc-595">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="856bc-596">쿠키 인증 미들웨어의 사용자 지정을 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 하 여 요청 간에 id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="856bc-597">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="856bc-598">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="856bc-598">Save the access token</span></span>

<span data-ttu-id="856bc-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="856bc-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="856bc-600">`SaveTokens`는 `false` 최종 인증 쿠키의 크기를 줄이기 위해 기본적으로로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="856bc-601">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="856bc-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="856bc-602">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="856bc-603">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d*s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="856bc-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="856bc-604">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="856bc-604">How to add additional custom tokens</span></span>

<span data-ttu-id="856bc-605">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="856bc-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="856bc-606">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="856bc-606">Creating and adding claims</span></span>

<span data-ttu-id="856bc-607">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="856bc-608">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="856bc-609">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="856bc-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="856bc-610">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="856bc-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="856bc-611">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="856bc-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="856bc-612">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="856bc-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="856bc-613">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="856bc-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="856bc-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="856bc-615">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="856bc-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="856bc-616">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="856bc-616">Additional resources</span></span>

* <span data-ttu-id="856bc-617">[dotnet/AspNetCore 공학적 AspNetCore Alsample 앱](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): 연결 된 샘플 앱은 [Dotnet/GitHub 리포지토리의](https://github.com/dotnet/AspNetCore) `master` 엔지니어링 분기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="856bc-618">분기에는 `master` ASP.NET Core의 다음 릴리스에 대해 활성 개발 중인 코드가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="856bc-619">ASP.NET Core의 릴리스 버전에 대 한 샘플 앱 버전을 보려면 **분기** 드롭다운 목록을 사용 하 여 릴리스 분기 (예:)를 선택 `release/{X.Y}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="856bc-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
