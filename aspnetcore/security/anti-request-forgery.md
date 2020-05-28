---
<span data-ttu-id="c8845-101">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-102">'Blazor'</span></span>
- <span data-ttu-id="c8845-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-103">'Identity'</span></span>
- <span data-ttu-id="c8845-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-105">'Razor'</span></span>
- <span data-ttu-id="c8845-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="c8845-107">ASP.NET Core에서 교차 사이트 요청 위조 (XSRF/CSRF) 공격 방지</span><span class="sxs-lookup"><span data-stu-id="c8845-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="c8845-108">작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c8845-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c8845-109">사이트 간 요청 위조 (XSRF 또는 CSRF 라고도 함)는 악의적인 웹 앱이 클라이언트 브라우저와 해당 브라우저를 신뢰 하는 웹 앱 간의 상호 작용에 영향을 줄 수 있는 웹 호스팅 앱에 대 한 공격입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="c8845-110">웹 브라우저에서 웹 사이트에 대 한 모든 요청과 함께 일부 형식의 인증 토큰을 자동으로 보내기 때문에 이러한 공격이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="c8845-111">이러한 형태의 악용은 공격이 사용자의 이전에 인증 된 세션을 활용 하기 때문에 *한 번 클릭 공격* 또는 *세션 riding* 라고도 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="c8845-112">CSRF 공격의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="c8845-113">사용자가 `www.good-banking-site.com` 폼 인증을 사용 하 여에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="c8845-114">서버가 사용자를 인증 하 고 인증 쿠키를 포함 하는 응답을 발급 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="c8845-115">사이트는 유효한 인증 쿠키를 사용 하 여 수신 하는 모든 요청을 신뢰 하기 때문에 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="c8845-116">사용자가 악의적인 사이트인를 방문 합니다 `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="c8845-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="c8845-117">악의적인 사이트인는 `www.bad-crook-site.com` 다음과 유사한 HTML 양식을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="c8845-118">양식이 `action` 악의적인 사이트가 아닌 취약 한 사이트에 게시 되는 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="c8845-119">CSRF의 "교차 사이트" 부분입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="c8845-120">사용자가 제출 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-120">The user selects the submit button.</span></span> <span data-ttu-id="c8845-121">브라우저가 요청을 만들고 요청 된 도메인에 대 한 인증 쿠키를 자동으로 포함 `www.good-banking-site.com` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="c8845-122">요청은 `www.good-banking-site.com` 사용자의 인증 컨텍스트를 사용 하 여 서버에서 실행 되며 인증 된 사용자가 수행할 수 있는 모든 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="c8845-123">사용자가 폼을 제출 하는 단추를 선택 하는 시나리오 외에도 악의적인 사이트는 다음과 같은 일을 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="c8845-124">양식을 자동으로 전송 하는 스크립트를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="c8845-125">양식 제출을 AJAX 요청으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="c8845-126">CSS를 사용 하 여 폼을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-126">Hide the form using CSS.</span></span>

<span data-ttu-id="c8845-127">이러한 대체 시나리오에는 처음에 악성 사이트를 방문 하는 것 외에 다른 작업 또는 사용자 입력이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="c8845-128">HTTPS를 사용 하는 경우 CSRF 공격을 방지 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="c8845-129">악의적인 사이트는 안전 하지 않은 요청을 보낼 수 있는 것과 같은 방법으로 요청을 쉽게 보낼 수 있습니다 `https://www.good-banking-site.com/` .</span><span class="sxs-lookup"><span data-stu-id="c8845-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="c8845-130">일부 공격은 GET 요청에 응답 하는 끝점을 대상으로 하며,이 경우 이미지 태그를 사용 하 여 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="c8845-131">이러한 형태의 공격은 이미지를 허용 하지만 JavaScript를 차단 하는 포럼 사이트에서 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="c8845-132">변수 또는 리소스가 변경 되는 GET 요청에 대 한 상태를 변경 하는 앱은 악의적인 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="c8845-133">**변경 상태가 안전 하지 않은 가져오기 요청입니다. 모범 사례는 GET 요청에 대 한 상태를 변경 하지 않는 것입니다.**</span><span class="sxs-lookup"><span data-stu-id="c8845-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="c8845-134">CSRF 공격은 인증에 쿠키를 사용 하는 웹 앱에 대해 다음과 같은 이유로 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="c8845-135">브라우저는 웹 앱에서 발급 한 쿠키를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="c8845-136">저장 된 쿠키에는 인증 된 사용자에 대 한 세션 쿠키가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="c8845-137">브라우저는 브라우저에서 앱에 대 한 요청이 생성 된 방법에 관계 없이 모든 요청에 대해 도메인에 연결 된 모든 쿠키를 웹 앱에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="c8845-138">그러나 CSRF 공격은 쿠키를 악용 하는 것으로 제한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="c8845-139">예를 들어, 기본 및 다이제스트 인증에도 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="c8845-140">사용자가 기본 또는 다이제스트 인증을 사용 하 여 로그인 한 후에는 세션이 종료 될 때까지 브라우저에서 자동으로 자격 증명을 보냅니다 &dagger; .</span><span class="sxs-lookup"><span data-stu-id="c8845-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="c8845-141">&dagger;이 컨텍스트에서 *세션* 은 사용자를 인증 하는 데 사용 되는 클라이언트 쪽 세션을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="c8845-142">서버 쪽 세션 또는 [ASP.NET Core 세션 미들웨어](xref:fundamentals/app-state)와 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="c8845-143">사용자는 예방 조치를 취하여 CSRF 취약성 으로부터 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="c8845-144">웹 앱 사용을 마치면 웹 앱에서 로그 오프 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="c8845-145">브라우저 쿠키를 주기적으로 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="c8845-146">그러나 CSRF 취약점은 기본적으로 최종 사용자가 아닌 웹 앱에 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="c8845-147">인증 기본 사항</span><span class="sxs-lookup"><span data-stu-id="c8845-147">Authentication fundamentals</span></span>

<span data-ttu-id="c8845-148">쿠키 기반 인증은 널리 사용 되는 인증 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="c8845-149">토큰 기반 인증 시스템은 특히 SPAs (단일 페이지 응용 프로그램)에 널리 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="c8845-150">쿠키 기반 인증</span><span class="sxs-lookup"><span data-stu-id="c8845-150">Cookie-based authentication</span></span>

<span data-ttu-id="c8845-151">사용자가 사용자 이름 및 암호를 사용 하 여 인증 하는 경우 인증 및 권한 부여에 사용할 수 있는 인증 티켓이 포함 된 토큰이 발급 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="c8845-152">토큰은 클라이언트에서 수행 하는 모든 요청과 함께 제공 되는 쿠키로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="c8845-153">쿠키를 생성 하 고 유효성을 검사 하는 것은 쿠키 인증 미들웨어에 의해 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="c8845-154">[미들웨어](xref:fundamentals/middleware/index) 는 사용자 보안 주체를 암호화 된 쿠키로 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="c8845-155">후속 요청에서 미들웨어는 쿠키의 유효성을 검사 하 고, 보안 주체를 다시 만들고, 보안 주체를 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)의 [사용자](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) 속성에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="c8845-156">토큰 기반 인증</span><span class="sxs-lookup"><span data-stu-id="c8845-156">Token-based authentication</span></span>

<span data-ttu-id="c8845-157">사용자가 인증 되 면 토큰 (위조 방지 토큰이 아님)이 발급 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="c8845-158">토큰에는 [클레임](/dotnet/framework/security/claims-based-identity-model) 형식의 사용자 정보 또는 앱이 앱에서 유지 관리 되는 사용자 상태를 가리키는 참조 토큰이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="c8845-159">사용자가 인증을 요구 하는 리소스에 액세스 하려고 하면 토큰은 전달자 토큰의 형태로 추가 권한 부여 헤더를 사용 하 여 앱으로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="c8845-160">그러면 앱 상태 비저장이 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-160">This makes the app stateless.</span></span> <span data-ttu-id="c8845-161">각 후속 요청에서 토큰은 서버 쪽 유효성 검사에 대 한 요청에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="c8845-162">이 토큰은 *암호화*되지 않습니다. *인코딩됩니다*.</span><span class="sxs-lookup"><span data-stu-id="c8845-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="c8845-163">서버에서 토큰은 정보에 액세스 하기 위해 디코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="c8845-164">후속 요청에서 토큰을 보내려면 브라우저의 로컬 저장소에 토큰을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="c8845-165">토큰이 브라우저의 로컬 저장소에 저장 된 경우 CSRF 취약성에 대해 걱정 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c8845-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="c8845-166">토큰을 쿠키에 저장 하면 CSRF가 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="c8845-167">자세한 내용은 GitHub 문제 [SPA 코드 샘플은 두 개의 쿠키를 추가](https://github.com/dotnet/AspNetCore.Docs/issues/13369)하는 방법을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c8845-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="c8845-168">한 도메인에서 호스트 되는 여러 앱</span><span class="sxs-lookup"><span data-stu-id="c8845-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="c8845-169">공유 호스팅 환경은 세션 하이재킹, 로그인 CSRF 및 기타 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="c8845-170">`example1.contoso.net`및 `example2.contoso.net` 는 서로 다른 호스트 이지만 도메인의 호스트 간에 암시적 트러스트 관계가 있습니다 `*.contoso.net` .</span><span class="sxs-lookup"><span data-stu-id="c8845-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="c8845-171">이 암시적 트러스트 관계를 사용 하면 신뢰할 수 없는 호스트가 서로 다른 쿠키에 영향을 줄 수 있습니다. AJAX 요청을 제어 하는 동일한 원본 정책이 반드시 HTTP 쿠키에 적용 되는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="c8845-172">동일한 도메인에서 호스트 되는 앱 간에 신뢰할 수 있는 쿠키를 이용 하는 공격은 도메인을 공유 하지 않도록 방지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="c8845-173">각 앱이 자체 도메인에서 호스트 되는 경우에는 악용할 암시적 쿠키 트러스트 관계가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="c8845-174">위조 방지 구성 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8845-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="c8845-175">ASP.NET Core은 [ASP.NET Core 데이터 보호](xref:security/data-protection/introduction)를 사용 하 여 위조 방지를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="c8845-176">서버 팜에서 작동 하도록 데이터 보호 스택을 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="c8845-177">자세한 내용은 [데이터 보호 구성](xref:security/data-protection/configuration/overview) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c8845-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c8845-178">에서 다음 Api 중 하나가 호출 될 때 위조 방지 미들웨어는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c8845-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8845-179">에서가 호출 되 면 위조 방지 미들웨어가 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 추가 됩니다. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="c8845-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="c8845-180">ASP.NET Core 2.0 이상에서 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 는 위조 방지 토큰을 HTML 양식 요소에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="c8845-181">파일의 다음 태그는 Razor 위조 방지 토큰을 자동으로 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="c8845-182">마찬가지로, [IHtmlHelper html.beginform](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) 는 폼의 메서드가 GET이 아닌 경우 기본적으로 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="c8845-183">HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성은 태그에 `<form>` 특성이 포함 되어 있고 다음 중 하나에 해당 하는 경우에 발생 합니다 `method="post"` .</span><span class="sxs-lookup"><span data-stu-id="c8845-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="c8845-184">Action 특성이 비어 있는 경우 ( `action=""` )</span><span class="sxs-lookup"><span data-stu-id="c8845-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="c8845-185">Action 특성을 제공 하지 않습니다 ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="c8845-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="c8845-186">HTML 양식 요소에 대 한 위조 방지 토큰 자동 생성을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="c8845-187">특성을 사용 하 여 위조 방지 토큰을 명시적으로 사용 하지 않도록 설정 합니다 `asp-antiforgery` .</span><span class="sxs-lookup"><span data-stu-id="c8845-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="c8845-188">Form 요소는 태그 도우미 [! 옵트아웃 기호](xref:mvc/views/tag-helpers/intro#opt-out)를 사용 하 여 태그 도우미에서 옵트아웃 (opt out) 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="c8845-189">뷰에서를 제거 합니다 `FormTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="c8845-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="c8845-190">뷰에 `FormTagHelper` 다음 지시문을 추가 하 여 뷰에서를 제거할 수 있습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="c8845-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="c8845-191">[ Razor 페이지](xref:razor-pages/index) 는 XSRF/csrf에서 자동으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="c8845-192">자세한 내용은 [XSRF/CSRF 및 Razor Pages](xref:razor-pages/index#xsrf)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c8845-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="c8845-193">CSRF 공격 으로부터 보호 하는 가장 일반적인 방법은 STP ( *동기화 장치 토큰 패턴* )를 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="c8845-194">사용자가 양식 데이터를 사용 하 여 페이지를 요청 하면 STP가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="c8845-195">서버는 현재 사용자의 id와 연결 된 토큰을 클라이언트에 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="c8845-196">클라이언트에서 확인을 위해 토큰을 서버에 다시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="c8845-197">서버가 인증 된 사용자의 id와 일치 하지 않는 토큰을 받으면 요청이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="c8845-198">토큰은 고유 하 고 예측할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="c8845-199">토큰을 사용 하 여 일련의 요청을 적절 하 게 시퀀싱 할 수도 있습니다. 예를 들어 1 페이지 > 페이지 2 > 페이지 3)에 대 한 요청 시퀀스를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="c8845-200">ASP.NET Core MVC 및 Razor 페이지 템플릿의 모든 양식은 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="c8845-201">다음 뷰 예제에서는 위조 방지 토큰을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="c8845-202">`<form>`HTML 도우미에서 태그 도우미를 사용 하지 않고 요소에 위조 방지 토큰을 명시적으로 추가 합니다 [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) .</span><span class="sxs-lookup"><span data-stu-id="c8845-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="c8845-203">위의 각 경우에 ASP.NET Core는 다음과 유사한 숨겨진 양식 필드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="c8845-204">위조 방지 토큰을 사용 하기 위한 세 가지 [필터](xref:mvc/controllers/filters) 를 포함 하는 ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c8845-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="c8845-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="c8845-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="c8845-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="c8845-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="c8845-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="c8845-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="c8845-208">위조 방지 옵션</span><span class="sxs-lookup"><span data-stu-id="c8845-208">Antiforgery options</span></span>

<span data-ttu-id="c8845-209">[위조 방지 옵션](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) 사용자 지정 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c8845-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="c8845-210">&dagger;`Cookie` [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) 클래스의 속성을 사용 하 여 위조 방지 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="c8845-211">옵션</span><span class="sxs-lookup"><span data-stu-id="c8845-211">Option</span></span> | <span data-ttu-id="c8845-212">설명</span><span class="sxs-lookup"><span data-stu-id="c8845-212">Description</span></span> |
| ---
<span data-ttu-id="c8845-213">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-214">'Blazor'</span></span>
- <span data-ttu-id="c8845-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-215">'Identity'</span></span>
- <span data-ttu-id="c8845-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-217">'Razor'</span></span>
- <span data-ttu-id="c8845-218">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-218">'SignalR' uid:</span></span> 

<span data-ttu-id="c8845-219">--- | ---제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-220">'Blazor'</span></span>
- <span data-ttu-id="c8845-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-221">'Identity'</span></span>
- <span data-ttu-id="c8845-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-223">'Razor'</span></span>
- <span data-ttu-id="c8845-224">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8845-225">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-226">'Blazor'</span></span>
- <span data-ttu-id="c8845-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-227">'Identity'</span></span>
- <span data-ttu-id="c8845-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-229">'Razor'</span></span>
- <span data-ttu-id="c8845-230">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8845-231">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-232">'Blazor'</span></span>
- <span data-ttu-id="c8845-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-233">'Identity'</span></span>
- <span data-ttu-id="c8845-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-235">'Razor'</span></span>
- <span data-ttu-id="c8845-236">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-236">'SignalR' uid:</span></span> 

<span data-ttu-id="c8845-237">------ | | [쿠키](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지 쿠키를 만드는 데 사용 되는 설정을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="c8845-238">| | [Formfieldname](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="c8845-239">| | [헤드 ername](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="c8845-240">이면 `null` 시스템은 폼 데이터만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="c8845-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="c8845-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="c8845-242">기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="c8845-243">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="c8845-244">옵션</span><span class="sxs-lookup"><span data-stu-id="c8845-244">Option</span></span> | <span data-ttu-id="c8845-245">설명</span><span class="sxs-lookup"><span data-stu-id="c8845-245">Description</span></span> |
| ---
<span data-ttu-id="c8845-246">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-247">'Blazor'</span></span>
- <span data-ttu-id="c8845-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-248">'Identity'</span></span>
- <span data-ttu-id="c8845-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-250">'Razor'</span></span>
- <span data-ttu-id="c8845-251">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-251">'SignalR' uid:</span></span> 

<span data-ttu-id="c8845-252">--- | ---제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-253">'Blazor'</span></span>
- <span data-ttu-id="c8845-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-254">'Identity'</span></span>
- <span data-ttu-id="c8845-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-256">'Razor'</span></span>
- <span data-ttu-id="c8845-257">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8845-258">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-259">'Blazor'</span></span>
- <span data-ttu-id="c8845-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-260">'Identity'</span></span>
- <span data-ttu-id="c8845-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-262">'Razor'</span></span>
- <span data-ttu-id="c8845-263">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c8845-264">제목: author: 설명: ms author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c8845-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c8845-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c8845-265">'Blazor'</span></span>
- <span data-ttu-id="c8845-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c8845-266">'Identity'</span></span>
- <span data-ttu-id="c8845-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c8845-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="c8845-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c8845-268">'Razor'</span></span>
- <span data-ttu-id="c8845-269">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c8845-269">'SignalR' uid:</span></span> 

<span data-ttu-id="c8845-270">------ | | [쿠키](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 위조 방지 쿠키를 만드는 데 사용 되는 설정을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="c8845-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | 쿠키의 도메인입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="c8845-272">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-272">Defaults to `null`.</span></span> <span data-ttu-id="c8845-273">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="c8845-274">쿠키를 대신 하는 것이 좋습니다. 도메인.</span><span class="sxs-lookup"><span data-stu-id="c8845-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="c8845-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | 쿠키의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="c8845-276">설정 하지 않으면 시스템에서 [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("로 시작 하는 고유한 이름을 생성 합니다. AspNetCore. ").</span><span class="sxs-lookup"><span data-stu-id="c8845-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="c8845-277">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="c8845-278">대신 Cookie.Name를 사용할 것을 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="c8845-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | 쿠키에 설정 된 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="c8845-280">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="c8845-281">권장 되는 대안은 Cookie. Path입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="c8845-282">| | [Formfieldname](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 위조 방지 시스템이 뷰에서 위조 방지 토큰을 렌더링 하는 데 사용 하는 숨겨진 양식 필드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="c8845-283">| | [헤드 ername](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 위조 방지 시스템에서 사용 하는 헤더의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="c8845-284">이면 `null` 시스템은 폼 데이터만 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="c8845-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 위조 방지 시스템에 HTTPS가 필요한 지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="c8845-286">이면 `true` HTTPS가 아닌 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="c8845-287">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-287">Defaults to `false`.</span></span> <span data-ttu-id="c8845-288">이 속성은 사용 되지 않으며 이후 버전에서 제거 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="c8845-289">대신 쿠키를 설정 하는 것이 좋습니다. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="c8845-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="c8845-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 헤더 생성을 표시 하지 않을 지 여부를 지정 합니다 `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="c8845-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="c8845-291">기본적으로 헤더는 "SAMEORIGIN" 값을 사용 하 여 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="c8845-292">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="c8845-293">자세한 내용은 [은 cookieauthenticationoptions.authenticationtype](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="c8845-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="c8845-294">I위조 방지를 사용 하 여 위조 방지 기능 구성</span><span class="sxs-lookup"><span data-stu-id="c8845-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="c8845-295">[I방지 위조](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 는 위조 방지 기능을 구성 하는 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="c8845-296">`IAntiforgery`클래스의 메서드에서 요청 될 수 있습니다 `Configure` `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c8845-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="c8845-297">다음 예제에서는 앱의 홈페이지에서 미들웨어를 사용 하 여 위조 방지 토큰을 생성 하 고 응답에 쿠키 (이 항목의 뒷부분에 설명 된 기본 각도 명명 규칙 사용)로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="c8845-298">위조 방지 유효성 검사 필요</span><span class="sxs-lookup"><span data-stu-id="c8845-298">Require antiforgery validation</span></span>

<span data-ttu-id="c8845-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) 은 개별 작업, 컨트롤러 또는 전역에 적용 될 수 있는 작업 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="c8845-300">이 필터가 적용 된 작업에 대 한 요청은 유효한 위조 방지 토큰을 포함 하지 않는 한 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="c8845-301">특성에는 `ValidateAntiForgeryToken` HTTP GET 요청을 포함 하 여 표시 되는 작업 메서드에 대 한 요청 토큰이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="c8845-302">특성을 `ValidateAntiForgeryToken` 앱의 컨트롤러에서 적용 하는 경우 특성을 사용 하 여 재정의할 수 있습니다 `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="c8845-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="c8845-303">ASP.NET Core는 위조 방지 토큰 추가를 지원 하지 않으므로 요청을 자동으로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="c8845-304">안전 하지 않은 HTTP 메서드에 대 한 위조 방지 토큰의 유효성을 자동으로 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="c8845-305">ASP.NET Core 앱은 safe HTTP 메서드 (GET, HEAD, OPTIONS 및 TRACE)에 대해 위조 방지 토큰을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="c8845-306">특성을 광범위 하 게 적용 한 `ValidateAntiForgeryToken` 다음 특성을 사용 하 여 재정의 하는 대신 `IgnoreAntiforgeryToken` [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 특성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="c8845-307">이 특성은 특성에서 동일 하 게 작동 `ValidateAntiForgeryToken` 합니다. 단, 다음 HTTP 메서드를 사용 하 여 생성 된 요청에 대 한 토큰은 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="c8845-308">GET</span><span class="sxs-lookup"><span data-stu-id="c8845-308">GET</span></span>
* <span data-ttu-id="c8845-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="c8845-309">HEAD</span></span>
* <span data-ttu-id="c8845-310">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="c8845-310">OPTIONS</span></span>
* <span data-ttu-id="c8845-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="c8845-311">TRACE</span></span>

<span data-ttu-id="c8845-312">비 API 시나리오에는을 광범위 하 게 사용 하는 것이 좋습니다 `AutoValidateAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="c8845-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="c8845-313">이렇게 하면 게시 작업은 기본적으로 보호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="c8845-314">대신, `ValidateAntiForgeryToken` 개별 작업 메서드에가 적용 되지 않는 한, 기본적으로 위조 방지 토큰을 무시 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="c8845-315">앱이 CSRF 공격에 취약 한 상태로 유지 하 여 POST 작업 메서드를 실수로 보호 되지 않은 상태로 남겨둘 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="c8845-316">모든 게시물은 위조 방지 토큰을 전송 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="c8845-317">Api는 토큰의 쿠키가 아닌 부분을 보내기 위한 자동 메커니즘을 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="c8845-318">구현은 클라이언트 코드 구현에 따라 달라질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="c8845-319">몇 가지 예가 아래에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-319">Some examples are shown below:</span></span>

<span data-ttu-id="c8845-320">클래스 수준 예제:</span><span class="sxs-lookup"><span data-stu-id="c8845-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="c8845-321">전역 예:</span><span class="sxs-lookup"><span data-stu-id="c8845-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8845-322">서비스. AddMvc (options => 옵션입니다. Filters. Add (new AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="c8845-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="c8845-323">전역 또는 컨트롤러 위조 방지 특성 재정의</span><span class="sxs-lookup"><span data-stu-id="c8845-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="c8845-324">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) 필터는 지정 된 작업 (또는 컨트롤러)에 위조 방지 토큰이 필요 하지 않도록 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="c8845-325">적용 되는 경우이 필터 `ValidateAntiForgeryToken` 는 `AutoValidateAntiforgeryToken` 더 높은 수준 (전역 또는 컨트롤러)으로 지정 된 필터를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="c8845-326">인증 후 토큰 새로 고침</span><span class="sxs-lookup"><span data-stu-id="c8845-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="c8845-327">사용자를 보기 또는 페이지 페이지로 리디렉션하여 사용자를 인증 한 후에 토큰을 새로 고쳐야 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="c8845-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="c8845-328">JavaScript, AJAX 및 SPAs</span><span class="sxs-lookup"><span data-stu-id="c8845-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="c8845-329">기존의 HTML 기반 앱에서 위조 방지 토큰은 숨겨진 양식 필드를 사용 하 여 서버로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="c8845-330">최신 JavaScript 기반 앱과 SPAs에서 많은 요청은 프로그래밍 방식으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="c8845-331">이러한 AJAX 요청은 다른 기술 (예: 요청 헤더 또는 쿠키)을 사용 하 여 토큰을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="c8845-332">쿠키를 사용 하 여 인증 토큰을 저장 하 고 서버에서 API 요청을 인증 하는 경우 CSRF를 사용 하면 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="c8845-333">로컬 저장소를 사용 하 여 토큰을 저장 하는 경우 로컬 저장소의 값이 모든 요청을 통해 서버에 자동으로 전송 되지 않으므로 CSRF 취약성이 완화 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="c8845-334">따라서 로컬 저장소를 사용 하 여 클라이언트에 위조 방지 토큰을 저장 하 고 요청 헤더로 토큰을 전송 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="c8845-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c8845-335">JavaScript</span></span>

<span data-ttu-id="c8845-336">보기에서 JavaScript를 사용 하 여 뷰 내에서 서비스를 사용 하 여 토큰을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="c8845-337">[AspNetCore](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) 를 뷰에 삽입 하 고 [Getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="c8845-338">이 방법을 사용 하면 서버에서 쿠키를 설정 하거나 클라이언트에서 쿠키를 읽을 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="c8845-339">앞의 예제에서는 JavaScript를 사용 하 여 AJAX POST 헤더에 대 한 숨겨진 필드 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="c8845-340">JavaScript는 또한 쿠키의 토큰에 액세스 하 고 쿠키의 내용을 사용 하 여 토큰 값을 포함 하는 헤더를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="c8845-341">스크립트가 라는 헤더에서 토큰을 보내도록 요청 하는 경우 `X-CSRF-TOKEN` 위조 방지 서비스를 구성 하 여 헤더를 찾습니다 `X-CSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="c8845-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="c8845-342">다음 예제에서는 JavaScript를 사용 하 여 적절 한 헤더를 사용 하는 AJAX 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="c8845-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="c8845-343">AngularJS</span></span>

<span data-ttu-id="c8845-344">AngularJS는 규칙을 사용 하 여 CSRF를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="c8845-345">서버에서 이름을 가진 쿠키를 전송 하는 경우 `XSRF-TOKEN` AngularJS `$http` 서비스는 서버에 요청을 보낼 때 헤더에 쿠키 값을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="c8845-346">이 프로세스는 자동입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-346">This process is automatic.</span></span> <span data-ttu-id="c8845-347">헤더는 클라이언트에서 명시적으로 설정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="c8845-348">헤더 이름은 `X-XSRF-TOKEN` 입니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="c8845-349">서버에서이 헤더를 검색 하 고 내용의 유효성을 검사 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="c8845-350">ASP.NET Core API가 응용 프로그램 시작 시이 규칙을 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="c8845-351">이라는 쿠키에 토큰을 제공 하도록 앱을 구성 `XSRF-TOKEN` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="c8845-352">라는 헤더를 찾도록 위조 방지 서비스를 구성 `X-XSRF-TOKEN` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="c8845-353">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8845-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="c8845-354">위조 방지 확장</span><span class="sxs-lookup"><span data-stu-id="c8845-354">Extend antiforgery</span></span>

<span data-ttu-id="c8845-355">[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 형식을 사용 하면 개발자가 각 토큰에서 추가 데이터를 라운드트립 하 여 csrf 시스템의 동작을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="c8845-356">[Getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) 메서드는 필드 토큰이 생성 될 때마다 호출 되며 반환 값은 생성 된 토큰에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="c8845-357">구현자는 타임 스탬프, nonce 또는 다른 값을 반환 하 고, 토큰의 유효성을 검사할 때 [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) 를 호출 하 여이 데이터의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="c8845-358">클라이언트의 사용자 이름이 이미 생성 된 토큰에 포함 되어 있으므로이 정보를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="c8845-359">토큰에 추가 데이터가 포함 되어 있지만가 구성 되지 않은 경우 `IAntiForgeryAdditionalDataProvider` 보충 데이터의 유효성이 검사 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8845-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c8845-360">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="c8845-360">Additional resources</span></span>

* <span data-ttu-id="c8845-361">OWASP ( [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) )의 [csrf](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))</span><span class="sxs-lookup"><span data-stu-id="c8845-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
