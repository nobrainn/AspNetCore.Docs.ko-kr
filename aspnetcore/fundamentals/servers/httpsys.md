---
<span data-ttu-id="c7c96-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-102">'Blazor'</span></span>
- <span data-ttu-id="c7c96-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-103">'Identity'</span></span>
- <span data-ttu-id="c7c96-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-105">'Razor'</span></span>
- <span data-ttu-id="c7c96-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-106">'SignalR' uid:</span></span> 

---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="c7c96-107">ASP.NET Core에서 HTTP.sys 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="c7c96-107">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="c7c96-108">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="c7c96-108">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="c7c96-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="c7c96-110">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-110">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c7c96-111">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-111">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-112">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-112">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="c7c96-113">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-113">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="c7c96-114">포트 공유</span><span class="sxs-lookup"><span data-stu-id="c7c96-114">Port sharing</span></span>
* <span data-ttu-id="c7c96-115">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7c96-115">HTTPS with SNI</span></span>
* <span data-ttu-id="c7c96-116">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-116">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="c7c96-117">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="c7c96-117">Direct file transmission</span></span>
* <span data-ttu-id="c7c96-118">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="c7c96-118">Response caching</span></span>
* <span data-ttu-id="c7c96-119">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-119">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="c7c96-120">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="c7c96-120">Supported Windows versions:</span></span>

* <span data-ttu-id="c7c96-121">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-121">Windows 7 or later</span></span>
* <span data-ttu-id="c7c96-122">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-122">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c7c96-123">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7c96-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="c7c96-124">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-124">When to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-125">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-125">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="c7c96-126">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-126">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="c7c96-128">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-128">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c7c96-130">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-130">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="c7c96-131">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-131">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7c96-132">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c7c96-132">HTTP/2 support</span></span>

<span data-ttu-id="c7c96-133">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-133">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7c96-134">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-134">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="c7c96-135">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-135">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7c96-136">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-136">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7c96-137">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-137">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c7c96-138">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-138">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c7c96-139">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-139">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="c7c96-140">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-140">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="c7c96-141">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-141">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="c7c96-142">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-142">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="c7c96-143">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-143">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="c7c96-144">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-144">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="c7c96-145">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-145">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="c7c96-146">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="c7c96-146">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="c7c96-147">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-147">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-148">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="c7c96-149">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-149">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="c7c96-150">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-150">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="c7c96-151">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="c7c96-151">**HTTP.sys options**</span></span>

| <span data-ttu-id="c7c96-152">속성</span><span class="sxs-lookup"><span data-stu-id="c7c96-152">Property</span></span> | <span data-ttu-id="c7c96-153">설명</span><span class="sxs-lookup"><span data-stu-id="c7c96-153">Description</span></span> | <span data-ttu-id="c7c96-154">기본값</span><span class="sxs-lookup"><span data-stu-id="c7c96-154">Default</span></span> |
| ---
<span data-ttu-id="c7c96-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-156">'Blazor'</span></span>
- <span data-ttu-id="c7c96-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-157">'Identity'</span></span>
- <span data-ttu-id="c7c96-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-159">'Razor'</span></span>
- <span data-ttu-id="c7c96-160">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-162">'Blazor'</span></span>
- <span data-ttu-id="c7c96-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-163">'Identity'</span></span>
- <span data-ttu-id="c7c96-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-165">'Razor'</span></span>
- <span data-ttu-id="c7c96-166">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-166">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-168">'Blazor'</span></span>
- <span data-ttu-id="c7c96-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-169">'Identity'</span></span>
- <span data-ttu-id="c7c96-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-171">'Razor'</span></span>
- <span data-ttu-id="c7c96-172">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-174">'Blazor'</span></span>
- <span data-ttu-id="c7c96-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-175">'Identity'</span></span>
- <span data-ttu-id="c7c96-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-177">'Razor'</span></span>
- <span data-ttu-id="c7c96-178">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-180">'Blazor'</span></span>
- <span data-ttu-id="c7c96-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-181">'Identity'</span></span>
- <span data-ttu-id="c7c96-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-183">'Razor'</span></span>
- <span data-ttu-id="c7c96-184">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-184">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="c7c96-186"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-186"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="c7c96-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="c7c96-188">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-188">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-189">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-189">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="c7c96-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="c7c96-191">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-191">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="c7c96-192">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-192">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="c7c96-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="c7c96-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="c7c96-195">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-195">Use `-1` for infinite.</span></span> <span data-ttu-id="c7c96-196">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-196">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="c7c96-197">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="c7c96-197">(machine-wide</span></span><br><span data-ttu-id="c7c96-198">설정) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-198">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="c7c96-199">| 30000000바이트</span><span class="sxs-lookup"><span data-stu-id="c7c96-199">| 30000000 bytes</span></span><br><span data-ttu-id="c7c96-200">(~28.6MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> |큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-200">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="c7c96-201">| 1000 | | `RequestQueueMode` | 이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-201">| 1000 | | `RequestQueueMode` | This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="c7c96-202">기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-202">Most existing configuration options do not apply when attaching to an existing queue.</span></span><span data-ttu-id="c7c96-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | HTTP.sys 요청 큐의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | The name of the HTTP.sys request queue.</span></span><span data-ttu-id="c7c96-204"> | `null`(익명 대기열) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-204"> | `null` (Anonymous queue) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="c7c96-205">(정상적으로 완료) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-205">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="c7c96-206">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-206">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="c7c96-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="c7c96-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="c7c96-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="c7c96-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="c7c96-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="c7c96-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="c7c96-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="c7c96-214">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-214">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="c7c96-215">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-215">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-216">|  |</span><span class="sxs-lookup"><span data-stu-id="c7c96-216">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="c7c96-217">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="c7c96-217">**MaxRequestBodySize**</span></span>

<span data-ttu-id="c7c96-218">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-218">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="c7c96-219">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-219">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="c7c96-220">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-220">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="c7c96-221">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-221">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7c96-222">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-222">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="c7c96-223">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-223">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7c96-224">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-224">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="c7c96-225">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-225">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-226">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-226">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="c7c96-227">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-227">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="c7c96-229">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-229">Configure Windows Server</span></span>

1. <span data-ttu-id="c7c96-230">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-230">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="c7c96-231">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-231">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-232">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-232">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="c7c96-233">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-233">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-234">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-234">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="c7c96-235">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-235">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="c7c96-236">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-236">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="c7c96-237">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-237">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="c7c96-238">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-238">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="c7c96-239">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-239">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="c7c96-240">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-240">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="c7c96-241">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-241">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="c7c96-242">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-242">Install the required .NET Framework.</span></span> <span data-ttu-id="c7c96-243">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-243">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="c7c96-244">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-244">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="c7c96-245">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-245">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="c7c96-246">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-246">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="c7c96-247">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-247">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c7c96-248">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-248">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="c7c96-249">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c7c96-249">`urls` command-line argument</span></span>
   * <span data-ttu-id="c7c96-250">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c7c96-250">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="c7c96-251">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-251">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="c7c96-252">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-252">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="c7c96-253">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-253">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="c7c96-254">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-254">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="c7c96-255">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-255">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="c7c96-256">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-256">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c7c96-257">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-257">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="c7c96-258">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-258">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c7c96-259">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-259">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="c7c96-260">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="c7c96-260">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c7c96-261">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-261">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="c7c96-262">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-262">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="c7c96-263">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-263">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="c7c96-264">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-264">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="c7c96-265">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-265">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="c7c96-266">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-266">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="c7c96-267">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-267">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="c7c96-268">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-268">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-269">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-269">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="c7c96-270">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="c7c96-270">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="c7c96-271">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-271">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="c7c96-272">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-272">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="c7c96-273">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-273">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="c7c96-274">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-274">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="c7c96-275">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-275">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="c7c96-276">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-276">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="c7c96-277">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-277">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="c7c96-278">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-278">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-279">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-279">Use a valid IP address.</span></span>
   * <span data-ttu-id="c7c96-280">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-280">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="c7c96-281">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-281">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="c7c96-282">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-282">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="c7c96-283">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-283">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="c7c96-284">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-284">In Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-285">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-285">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="c7c96-286">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-286">Select the **Package** tab.</span></span>
     * <span data-ttu-id="c7c96-287">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-287">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="c7c96-288">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="c7c96-288">When not using Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-289">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-289">Open the app's project file.</span></span>
     * <span data-ttu-id="c7c96-290">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-290">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="c7c96-291">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="c7c96-291">In the following example:</span></span>

   * <span data-ttu-id="c7c96-292">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-292">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="c7c96-293">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-293">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="c7c96-294">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-294">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="c7c96-295">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-295">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="c7c96-296">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-296">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="c7c96-297">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="c7c96-297">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="c7c96-298">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="c7c96-298">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="c7c96-299">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-299">Run the app.</span></span>

   <span data-ttu-id="c7c96-300">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-300">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="c7c96-301">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-301">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="c7c96-302">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-302">The app responds at the server's public IP address.</span></span> <span data-ttu-id="c7c96-303">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-303">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="c7c96-304">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-304">A development certificate is used in this example.</span></span> <span data-ttu-id="c7c96-305">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-305">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c7c96-307">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="c7c96-307">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c7c96-308">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-308">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="c7c96-309">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-309">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7c96-310">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c7c96-310">Additional resources</span></span>

* [<span data-ttu-id="c7c96-311">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c7c96-311">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="c7c96-312">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="c7c96-312">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)</span></span>
* [<span data-ttu-id="c7c96-313">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="c7c96-313">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="c7c96-314">호스트</span><span class="sxs-lookup"><span data-stu-id="c7c96-314">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="c7c96-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="c7c96-316">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-316">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c7c96-317">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-317">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-318">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-318">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="c7c96-319">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-319">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="c7c96-320">포트 공유</span><span class="sxs-lookup"><span data-stu-id="c7c96-320">Port sharing</span></span>
* <span data-ttu-id="c7c96-321">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7c96-321">HTTPS with SNI</span></span>
* <span data-ttu-id="c7c96-322">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-322">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="c7c96-323">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="c7c96-323">Direct file transmission</span></span>
* <span data-ttu-id="c7c96-324">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="c7c96-324">Response caching</span></span>
* <span data-ttu-id="c7c96-325">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-325">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="c7c96-326">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="c7c96-326">Supported Windows versions:</span></span>

* <span data-ttu-id="c7c96-327">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-327">Windows 7 or later</span></span>
* <span data-ttu-id="c7c96-328">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-328">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c7c96-329">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7c96-329">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="c7c96-330">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-330">When to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-331">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-331">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="c7c96-332">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-332">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="c7c96-334">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-334">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c7c96-336">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-336">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="c7c96-337">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-337">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7c96-338">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c7c96-338">HTTP/2 support</span></span>

<span data-ttu-id="c7c96-339">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-339">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7c96-340">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-340">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="c7c96-341">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-341">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7c96-342">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-342">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7c96-343">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-343">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c7c96-344">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-344">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c7c96-345">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-345">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="c7c96-346">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-346">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="c7c96-347">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-347">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="c7c96-348">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-348">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="c7c96-349">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-349">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="c7c96-350">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-350">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="c7c96-351">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-351">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="c7c96-352">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="c7c96-352">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="c7c96-353">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-353">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-354">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-354">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="c7c96-355">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-355">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="c7c96-356">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-356">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="c7c96-357">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="c7c96-357">**HTTP.sys options**</span></span>

| <span data-ttu-id="c7c96-358">속성</span><span class="sxs-lookup"><span data-stu-id="c7c96-358">Property</span></span> | <span data-ttu-id="c7c96-359">설명</span><span class="sxs-lookup"><span data-stu-id="c7c96-359">Description</span></span> | <span data-ttu-id="c7c96-360">기본값</span><span class="sxs-lookup"><span data-stu-id="c7c96-360">Default</span></span> |
| ---
<span data-ttu-id="c7c96-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-362">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-362">'Blazor'</span></span>
- <span data-ttu-id="c7c96-363">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-363">'Identity'</span></span>
- <span data-ttu-id="c7c96-364">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-364">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-365">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-365">'Razor'</span></span>
- <span data-ttu-id="c7c96-366">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-366">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-368">'Blazor'</span></span>
- <span data-ttu-id="c7c96-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-369">'Identity'</span></span>
- <span data-ttu-id="c7c96-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-371">'Razor'</span></span>
- <span data-ttu-id="c7c96-372">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-372">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-374">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-374">'Blazor'</span></span>
- <span data-ttu-id="c7c96-375">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-375">'Identity'</span></span>
- <span data-ttu-id="c7c96-376">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-376">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-377">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-377">'Razor'</span></span>
- <span data-ttu-id="c7c96-378">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-378">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-380">'Blazor'</span></span>
- <span data-ttu-id="c7c96-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-381">'Identity'</span></span>
- <span data-ttu-id="c7c96-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-383">'Razor'</span></span>
- <span data-ttu-id="c7c96-384">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-386">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-386">'Blazor'</span></span>
- <span data-ttu-id="c7c96-387">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-387">'Identity'</span></span>
- <span data-ttu-id="c7c96-388">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-388">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-389">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-389">'Razor'</span></span>
- <span data-ttu-id="c7c96-390">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-390">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="c7c96-392"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-392"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="c7c96-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="c7c96-394">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-394">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-395">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-395">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="c7c96-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="c7c96-397">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-397">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="c7c96-398">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-398">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="c7c96-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="c7c96-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="c7c96-401">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-401">Use `-1` for infinite.</span></span> <span data-ttu-id="c7c96-402">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-402">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="c7c96-403">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="c7c96-403">(machine-wide</span></span><br><span data-ttu-id="c7c96-404">설정) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-404">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="c7c96-405">| 30000000바이트</span><span class="sxs-lookup"><span data-stu-id="c7c96-405">| 30000000 bytes</span></span><br><span data-ttu-id="c7c96-406">(~28.6MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> |큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-406">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="c7c96-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="c7c96-408">(정상적으로 완료) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-408">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="c7c96-409">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-409">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="c7c96-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="c7c96-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="c7c96-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="c7c96-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="c7c96-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="c7c96-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="c7c96-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="c7c96-417">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-417">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="c7c96-418">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-418">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-419">|  |</span><span class="sxs-lookup"><span data-stu-id="c7c96-419">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="c7c96-420">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="c7c96-420">**MaxRequestBodySize**</span></span>

<span data-ttu-id="c7c96-421">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-421">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="c7c96-422">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-422">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="c7c96-423">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-423">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="c7c96-424">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-424">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7c96-425">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-425">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="c7c96-426">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-426">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7c96-427">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-427">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="c7c96-428">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-428">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-429">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-429">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="c7c96-430">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-430">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="c7c96-432">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-432">Configure Windows Server</span></span>

1. <span data-ttu-id="c7c96-433">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-433">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="c7c96-434">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-434">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-435">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-435">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="c7c96-436">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-436">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-437">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-437">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="c7c96-438">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-438">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="c7c96-439">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-439">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="c7c96-440">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-440">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="c7c96-441">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-441">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="c7c96-442">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-442">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="c7c96-443">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-443">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="c7c96-444">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-444">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="c7c96-445">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-445">Install the required .NET Framework.</span></span> <span data-ttu-id="c7c96-446">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-446">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="c7c96-447">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-447">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="c7c96-448">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-448">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="c7c96-449">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-449">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="c7c96-450">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-450">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c7c96-451">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-451">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="c7c96-452">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c7c96-452">`urls` command-line argument</span></span>
   * <span data-ttu-id="c7c96-453">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c7c96-453">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="c7c96-454">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-454">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="c7c96-455">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-455">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="c7c96-456">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-456">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="c7c96-457">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-457">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="c7c96-458">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-458">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="c7c96-459">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-459">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c7c96-460">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-460">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="c7c96-461">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-461">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c7c96-462">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-462">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="c7c96-463">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="c7c96-463">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c7c96-464">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-464">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="c7c96-465">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-465">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="c7c96-466">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-466">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="c7c96-467">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-467">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="c7c96-468">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-468">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="c7c96-469">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-469">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="c7c96-470">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-470">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="c7c96-471">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-471">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-472">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-472">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="c7c96-473">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="c7c96-473">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="c7c96-474">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-474">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="c7c96-475">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-475">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="c7c96-476">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-476">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="c7c96-477">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-477">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="c7c96-478">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-478">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="c7c96-479">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-479">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="c7c96-480">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-480">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="c7c96-481">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-481">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-482">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-482">Use a valid IP address.</span></span>
   * <span data-ttu-id="c7c96-483">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-483">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="c7c96-484">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-484">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="c7c96-485">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-485">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="c7c96-486">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-486">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="c7c96-487">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-487">In Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-488">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-488">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="c7c96-489">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-489">Select the **Package** tab.</span></span>
     * <span data-ttu-id="c7c96-490">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-490">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="c7c96-491">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="c7c96-491">When not using Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-492">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-492">Open the app's project file.</span></span>
     * <span data-ttu-id="c7c96-493">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-493">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="c7c96-494">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="c7c96-494">In the following example:</span></span>

   * <span data-ttu-id="c7c96-495">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-495">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="c7c96-496">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-496">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="c7c96-497">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-497">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="c7c96-498">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-498">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="c7c96-499">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-499">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="c7c96-500">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="c7c96-500">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="c7c96-501">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="c7c96-501">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="c7c96-502">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-502">Run the app.</span></span>

   <span data-ttu-id="c7c96-503">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-503">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="c7c96-504">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-504">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="c7c96-505">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-505">The app responds at the server's public IP address.</span></span> <span data-ttu-id="c7c96-506">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-506">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="c7c96-507">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-507">A development certificate is used in this example.</span></span> <span data-ttu-id="c7c96-508">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-508">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c7c96-510">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="c7c96-510">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c7c96-511">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-511">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="c7c96-512">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-512">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7c96-513">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c7c96-513">Additional resources</span></span>

* [<span data-ttu-id="c7c96-514">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c7c96-514">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="c7c96-515">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="c7c96-515">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)</span></span>
* [<span data-ttu-id="c7c96-516">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="c7c96-516">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="c7c96-517">호스트</span><span class="sxs-lookup"><span data-stu-id="c7c96-517">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c7c96-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="c7c96-519">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-519">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c7c96-520">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-520">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-521">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-521">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="c7c96-522">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-522">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="c7c96-523">포트 공유</span><span class="sxs-lookup"><span data-stu-id="c7c96-523">Port sharing</span></span>
* <span data-ttu-id="c7c96-524">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7c96-524">HTTPS with SNI</span></span>
* <span data-ttu-id="c7c96-525">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-525">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="c7c96-526">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="c7c96-526">Direct file transmission</span></span>
* <span data-ttu-id="c7c96-527">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="c7c96-527">Response caching</span></span>
* <span data-ttu-id="c7c96-528">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-528">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="c7c96-529">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="c7c96-529">Supported Windows versions:</span></span>

* <span data-ttu-id="c7c96-530">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-530">Windows 7 or later</span></span>
* <span data-ttu-id="c7c96-531">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-531">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c7c96-532">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7c96-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="c7c96-533">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-533">When to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-534">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-534">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="c7c96-535">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-535">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="c7c96-537">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-537">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c7c96-539">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-539">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="c7c96-540">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-540">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7c96-541">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c7c96-541">HTTP/2 support</span></span>

<span data-ttu-id="c7c96-542">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-542">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7c96-543">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-543">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="c7c96-544">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-544">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7c96-545">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-545">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7c96-546">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-546">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="c7c96-547">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-547">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c7c96-548">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-548">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="c7c96-549">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-549">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="c7c96-550">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-550">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="c7c96-551">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-551">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="c7c96-552">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-552">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="c7c96-553">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-553">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="c7c96-554">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-554">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="c7c96-555">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="c7c96-555">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="c7c96-556">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-556">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-557">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-557">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="c7c96-558">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-558">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="c7c96-559">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-559">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="c7c96-560">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-560">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="c7c96-561">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-561">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="c7c96-562">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="c7c96-562">**HTTP.sys options**</span></span>

| <span data-ttu-id="c7c96-563">속성</span><span class="sxs-lookup"><span data-stu-id="c7c96-563">Property</span></span> | <span data-ttu-id="c7c96-564">설명</span><span class="sxs-lookup"><span data-stu-id="c7c96-564">Description</span></span> | <span data-ttu-id="c7c96-565">기본값</span><span class="sxs-lookup"><span data-stu-id="c7c96-565">Default</span></span> |
| ---
<span data-ttu-id="c7c96-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-567">'Blazor'</span></span>
- <span data-ttu-id="c7c96-568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-568">'Identity'</span></span>
- <span data-ttu-id="c7c96-569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-569">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-570">'Razor'</span></span>
- <span data-ttu-id="c7c96-571">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-573">'Blazor'</span></span>
- <span data-ttu-id="c7c96-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-574">'Identity'</span></span>
- <span data-ttu-id="c7c96-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-576">'Razor'</span></span>
- <span data-ttu-id="c7c96-577">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-577">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-579">'Blazor'</span></span>
- <span data-ttu-id="c7c96-580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-580">'Identity'</span></span>
- <span data-ttu-id="c7c96-581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-581">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-582">'Razor'</span></span>
- <span data-ttu-id="c7c96-583">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-585">'Blazor'</span></span>
- <span data-ttu-id="c7c96-586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-586">'Identity'</span></span>
- <span data-ttu-id="c7c96-587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-587">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-588">'Razor'</span></span>
- <span data-ttu-id="c7c96-589">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-591">'Blazor'</span></span>
- <span data-ttu-id="c7c96-592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-592">'Identity'</span></span>
- <span data-ttu-id="c7c96-593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-593">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-594">'Razor'</span></span>
- <span data-ttu-id="c7c96-595">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-595">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="c7c96-597"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-597"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="c7c96-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="c7c96-599">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-599">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-600">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-600">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="c7c96-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="c7c96-602">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-602">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="c7c96-603">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-603">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="c7c96-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="c7c96-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="c7c96-606">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-606">Use `-1` for infinite.</span></span> <span data-ttu-id="c7c96-607">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-607">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="c7c96-608">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="c7c96-608">(machine-wide</span></span><br><span data-ttu-id="c7c96-609">설정) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-609">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="c7c96-610">| 30000000바이트</span><span class="sxs-lookup"><span data-stu-id="c7c96-610">| 30000000 bytes</span></span><br><span data-ttu-id="c7c96-611">(~28.6MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> |큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-611">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="c7c96-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="c7c96-613">(정상적으로 완료) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-613">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="c7c96-614">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-614">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="c7c96-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="c7c96-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="c7c96-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="c7c96-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="c7c96-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="c7c96-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="c7c96-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="c7c96-622">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-622">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="c7c96-623">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-623">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-624">|  |</span><span class="sxs-lookup"><span data-stu-id="c7c96-624">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="c7c96-625">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="c7c96-625">**MaxRequestBodySize**</span></span>

<span data-ttu-id="c7c96-626">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-626">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="c7c96-627">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-627">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="c7c96-628">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-628">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="c7c96-629">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-629">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7c96-630">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-630">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="c7c96-631">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-631">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7c96-632">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-632">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="c7c96-633">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-633">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-634">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-634">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="c7c96-635">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-635">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="c7c96-637">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-637">Configure Windows Server</span></span>

1. <span data-ttu-id="c7c96-638">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-638">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="c7c96-639">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-639">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-640">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-640">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="c7c96-641">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-641">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-642">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-642">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="c7c96-643">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-643">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="c7c96-644">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-644">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="c7c96-645">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-645">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="c7c96-646">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-646">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="c7c96-647">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-647">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="c7c96-648">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-648">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="c7c96-649">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-649">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="c7c96-650">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-650">Install the required .NET Framework.</span></span> <span data-ttu-id="c7c96-651">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-651">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="c7c96-652">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-652">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="c7c96-653">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-653">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="c7c96-654">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-654">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="c7c96-655">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-655">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c7c96-656">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-656">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="c7c96-657">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c7c96-657">`urls` command-line argument</span></span>
   * <span data-ttu-id="c7c96-658">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c7c96-658">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="c7c96-659">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-659">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="c7c96-660">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-660">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="c7c96-661">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-661">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="c7c96-662">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-662">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="c7c96-663">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-663">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="c7c96-664">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-664">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c7c96-665">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-665">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="c7c96-666">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-666">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c7c96-667">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-667">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="c7c96-668">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="c7c96-668">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c7c96-669">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-669">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="c7c96-670">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-670">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="c7c96-671">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-671">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="c7c96-672">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-672">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="c7c96-673">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-673">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="c7c96-674">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-674">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="c7c96-675">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-675">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="c7c96-676">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-676">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-677">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-677">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="c7c96-678">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="c7c96-678">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="c7c96-679">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-679">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="c7c96-680">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-680">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="c7c96-681">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-681">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="c7c96-682">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-682">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="c7c96-683">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-683">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="c7c96-684">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-684">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="c7c96-685">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-685">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="c7c96-686">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-686">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-687">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-687">Use a valid IP address.</span></span>
   * <span data-ttu-id="c7c96-688">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-688">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="c7c96-689">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-689">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="c7c96-690">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-690">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="c7c96-691">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-691">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="c7c96-692">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-692">In Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-693">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-693">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="c7c96-694">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-694">Select the **Package** tab.</span></span>
     * <span data-ttu-id="c7c96-695">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-695">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="c7c96-696">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="c7c96-696">When not using Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-697">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-697">Open the app's project file.</span></span>
     * <span data-ttu-id="c7c96-698">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-698">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="c7c96-699">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="c7c96-699">In the following example:</span></span>

   * <span data-ttu-id="c7c96-700">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-700">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="c7c96-701">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-701">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="c7c96-702">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-702">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="c7c96-703">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-703">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="c7c96-704">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-704">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="c7c96-705">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="c7c96-705">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="c7c96-706">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="c7c96-706">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="c7c96-707">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-707">Run the app.</span></span>

   <span data-ttu-id="c7c96-708">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-708">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="c7c96-709">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-709">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="c7c96-710">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-710">The app responds at the server's public IP address.</span></span> <span data-ttu-id="c7c96-711">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-711">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="c7c96-712">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-712">A development certificate is used in this example.</span></span> <span data-ttu-id="c7c96-713">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-713">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c7c96-715">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="c7c96-715">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c7c96-716">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-716">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="c7c96-717">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-717">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7c96-718">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c7c96-718">Additional resources</span></span>

* [<span data-ttu-id="c7c96-719">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c7c96-719">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="c7c96-720">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="c7c96-720">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)</span></span>
* [<span data-ttu-id="c7c96-721">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="c7c96-721">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="c7c96-722">호스트</span><span class="sxs-lookup"><span data-stu-id="c7c96-722">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c7c96-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="c7c96-724">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-724">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c7c96-725">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-725">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-726">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-726">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="c7c96-727">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-727">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="c7c96-728">포트 공유</span><span class="sxs-lookup"><span data-stu-id="c7c96-728">Port sharing</span></span>
* <span data-ttu-id="c7c96-729">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="c7c96-729">HTTPS with SNI</span></span>
* <span data-ttu-id="c7c96-730">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-730">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="c7c96-731">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="c7c96-731">Direct file transmission</span></span>
* <span data-ttu-id="c7c96-732">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="c7c96-732">Response caching</span></span>
* <span data-ttu-id="c7c96-733">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="c7c96-733">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="c7c96-734">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="c7c96-734">Supported Windows versions:</span></span>

* <span data-ttu-id="c7c96-735">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-735">Windows 7 or later</span></span>
* <span data-ttu-id="c7c96-736">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-736">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c7c96-737">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c7c96-737">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="c7c96-738">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-738">When to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-739">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-739">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="c7c96-740">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c7c96-740">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="c7c96-742">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-742">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="c7c96-744">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-744">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="c7c96-745">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-745">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="c7c96-746">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="c7c96-746">HTTP/2 support</span></span>

<span data-ttu-id="c7c96-747">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-747">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="c7c96-748">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="c7c96-748">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="c7c96-749">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-749">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="c7c96-750">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="c7c96-750">TLS 1.2 or later connection</span></span>

<span data-ttu-id="c7c96-751">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-751">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="c7c96-752">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-752">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="c7c96-753">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-753">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="c7c96-754">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-754">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="c7c96-755">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="c7c96-755">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="c7c96-756">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-756">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="c7c96-757">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-757">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="c7c96-758">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-758">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="c7c96-759">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-759">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="c7c96-760">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="c7c96-760">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="c7c96-761">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-761">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="c7c96-762">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-762">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="c7c96-763">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-763">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="c7c96-764">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-764">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="c7c96-765">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-765">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="c7c96-766">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-766">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="c7c96-767">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="c7c96-767">**HTTP.sys options**</span></span>

| <span data-ttu-id="c7c96-768">속성</span><span class="sxs-lookup"><span data-stu-id="c7c96-768">Property</span></span> | <span data-ttu-id="c7c96-769">설명</span><span class="sxs-lookup"><span data-stu-id="c7c96-769">Description</span></span> | <span data-ttu-id="c7c96-770">기본값</span><span class="sxs-lookup"><span data-stu-id="c7c96-770">Default</span></span> |
| ---
<span data-ttu-id="c7c96-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-772">'Blazor'</span></span>
- <span data-ttu-id="c7c96-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-773">'Identity'</span></span>
- <span data-ttu-id="c7c96-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-775">'Razor'</span></span>
- <span data-ttu-id="c7c96-776">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-778">'Blazor'</span></span>
- <span data-ttu-id="c7c96-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-779">'Identity'</span></span>
- <span data-ttu-id="c7c96-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-781">'Razor'</span></span>
- <span data-ttu-id="c7c96-782">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-782">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-784">'Blazor'</span></span>
- <span data-ttu-id="c7c96-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-785">'Identity'</span></span>
- <span data-ttu-id="c7c96-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-787">'Razor'</span></span>
- <span data-ttu-id="c7c96-788">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-790">'Blazor'</span></span>
- <span data-ttu-id="c7c96-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-791">'Identity'</span></span>
- <span data-ttu-id="c7c96-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-793">'Razor'</span></span>
- <span data-ttu-id="c7c96-794">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c7c96-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c7c96-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c7c96-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-796">'Blazor'</span></span>
- <span data-ttu-id="c7c96-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c7c96-797">'Identity'</span></span>
- <span data-ttu-id="c7c96-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c7c96-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="c7c96-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c7c96-799">'Razor'</span></span>
- <span data-ttu-id="c7c96-800">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c7c96-800">'SignalR' uid:</span></span> 

<span data-ttu-id="c7c96-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | `HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="c7c96-802"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-802"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="c7c96-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="c7c96-804">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-804">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-805">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-805">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="c7c96-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="c7c96-807">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-807">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="c7c96-808">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-808">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="c7c96-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="c7c96-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="c7c96-811">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-811">Use `-1` for infinite.</span></span> <span data-ttu-id="c7c96-812">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-812">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="c7c96-813">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="c7c96-813">(machine-wide</span></span><br><span data-ttu-id="c7c96-814">설정) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-814">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="c7c96-815">| 30000000바이트</span><span class="sxs-lookup"><span data-stu-id="c7c96-815">| 30000000 bytes</span></span><br><span data-ttu-id="c7c96-816">(~28.6MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> |큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-816">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="c7c96-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="c7c96-818">(정상적으로 완료) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-818">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="c7c96-819">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-819">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="c7c96-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="c7c96-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="c7c96-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="c7c96-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="c7c96-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="c7c96-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="c7c96-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="c7c96-827">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-827">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="c7c96-828">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-828">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="c7c96-829">|  |</span><span class="sxs-lookup"><span data-stu-id="c7c96-829">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="c7c96-830">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="c7c96-830">**MaxRequestBodySize**</span></span>

<span data-ttu-id="c7c96-831">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-831">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="c7c96-832">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-832">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="c7c96-833">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-833">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="c7c96-834">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-834">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="c7c96-835">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-835">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="c7c96-836">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-836">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="c7c96-837">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-837">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="c7c96-838">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-838">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="c7c96-839">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-839">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="c7c96-840">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-840">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="c7c96-842">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="c7c96-842">Configure Windows Server</span></span>

1. <span data-ttu-id="c7c96-843">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-843">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="c7c96-844">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-844">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-845">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-845">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="c7c96-846">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-846">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="c7c96-847">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-847">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="c7c96-848">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-848">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="c7c96-849">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-849">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="c7c96-850">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-850">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="c7c96-851">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-851">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="c7c96-852">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-852">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="c7c96-853">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-853">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="c7c96-854">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-854">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="c7c96-855">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-855">Install the required .NET Framework.</span></span> <span data-ttu-id="c7c96-856">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-856">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="c7c96-857">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-857">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="c7c96-858">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-858">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="c7c96-859">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-859">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="c7c96-860">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-860">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c7c96-861">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-861">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="c7c96-862">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="c7c96-862">`urls` command-line argument</span></span>
   * <span data-ttu-id="c7c96-863">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="c7c96-863">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="c7c96-864">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-864">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="c7c96-865">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-865">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="c7c96-866">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-866">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="c7c96-867">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-867">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="c7c96-868">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-868">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="c7c96-869">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-869">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="c7c96-870">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-870">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="c7c96-871">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-871">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="c7c96-872">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-872">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="c7c96-873">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="c7c96-873">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="c7c96-874">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-874">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="c7c96-875">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-875">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="c7c96-876">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-876">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="c7c96-877">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-877">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="c7c96-878">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-878">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="c7c96-879">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-879">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="c7c96-880">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-880">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="c7c96-881">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-881">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-882">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-882">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="c7c96-883">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="c7c96-883">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="c7c96-884">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-884">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="c7c96-885">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-885">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="c7c96-886">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-886">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="c7c96-887">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-887">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="c7c96-888">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-888">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="c7c96-889">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-889">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="c7c96-890">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-890">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="c7c96-891">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-891">Don't use a wildcard binding.</span></span> <span data-ttu-id="c7c96-892">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-892">Use a valid IP address.</span></span>
   * <span data-ttu-id="c7c96-893">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-893">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="c7c96-894">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-894">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="c7c96-895">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-895">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="c7c96-896">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-896">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="c7c96-897">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-897">In Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-898">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-898">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="c7c96-899">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-899">Select the **Package** tab.</span></span>
     * <span data-ttu-id="c7c96-900">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-900">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="c7c96-901">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="c7c96-901">When not using Visual Studio:</span></span>
     * <span data-ttu-id="c7c96-902">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-902">Open the app's project file.</span></span>
     * <span data-ttu-id="c7c96-903">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-903">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="c7c96-904">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="c7c96-904">In the following example:</span></span>

   * <span data-ttu-id="c7c96-905">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-905">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="c7c96-906">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-906">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="c7c96-907">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-907">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="c7c96-908">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-908">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="c7c96-909">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-909">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="c7c96-910">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="c7c96-910">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="c7c96-911">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="c7c96-911">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="c7c96-912">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-912">Run the app.</span></span>

   <span data-ttu-id="c7c96-913">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-913">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="c7c96-914">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-914">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="c7c96-915">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-915">The app responds at the server's public IP address.</span></span> <span data-ttu-id="c7c96-916">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-916">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="c7c96-917">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-917">A development certificate is used in this example.</span></span> <span data-ttu-id="c7c96-918">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-918">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c7c96-920">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="c7c96-920">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c7c96-921">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c7c96-921">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="c7c96-922">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c7c96-922">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c7c96-923">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c7c96-923">Additional resources</span></span>

* [<span data-ttu-id="c7c96-924">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="c7c96-924">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="c7c96-925">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="c7c96-925">[HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)</span></span>
* [<span data-ttu-id="c7c96-926">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="c7c96-926">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="c7c96-927">호스트</span><span class="sxs-lookup"><span data-stu-id="c7c96-927">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
