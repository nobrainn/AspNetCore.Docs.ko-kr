---
<span data-ttu-id="570d0-101">제목: ' ASP.NET Core SignalR ' 작성자: 설명: ' ASP.NET Core 앱에서 진단을 수집 하는 방법을 알아봅니다 SignalR . '</span><span class="sxs-lookup"><span data-stu-id="570d0-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="570d0-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="570d0-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="570d0-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="570d0-103">'Blazor'</span></span>
- <span data-ttu-id="570d0-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="570d0-104">'Identity'</span></span>
- <span data-ttu-id="570d0-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="570d0-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="570d0-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="570d0-106">'Razor'</span></span>
- <span data-ttu-id="570d0-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="570d0-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="570d0-108">ASP.NET Core의 로깅 및 진단SignalR</span><span class="sxs-lookup"><span data-stu-id="570d0-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="570d0-109">[Andrew Stanton-간호사](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="570d0-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="570d0-110">이 문서에서는 문제 해결을 위해 ASP.NET Core 앱에서 진단 정보를 수집 하기 위한 지침 SignalR 을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="570d0-111">서버 쪽 로깅</span><span class="sxs-lookup"><span data-stu-id="570d0-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="570d0-112">서버 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="570d0-113">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="570d0-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="570d0-114">SignalR는 ASP.NET Core의 일부 이므로 ASP.NET Core 로깅 시스템을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="570d0-115">기본 구성에서는 SignalR 매우 적은 정보를 기록 하지만이를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="570d0-116">ASP.NET Core 로깅 구성에 대 한 자세한 내용은 [ASP.NET Core 로깅](xref:fundamentals/logging/index#configuration) 에 대 한 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="570d0-117">에서는 두 개의로 거 범주를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-117"> uses two logger categories:</span></span>

* <span data-ttu-id="570d0-118">`Microsoft.AspNetCore.SignalR`: 허브 프로토콜과 관련 된 로그, 허브 활성화, 메서드 호출 및 기타 허브 관련 작업</span><span class="sxs-lookup"><span data-stu-id="570d0-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="570d0-119">`Microsoft.AspNetCore.Http.Connections`: Websocket, 긴 폴링, 서버에서 보낸 이벤트, 하위 수준 인프라 등의 전송과 관련 된 로그 SignalR</span><span class="sxs-lookup"><span data-stu-id="570d0-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="570d0-120">에서 자세한 로그를 사용 하도록 설정 하려면 SignalR `Debug` 의 하위 섹션에 다음 항목을 추가 하 여 위의 접두사를 *appsettings* 파일의 수준으로 구성 합니다 `LogLevel` `Logging` .</span><span class="sxs-lookup"><span data-stu-id="570d0-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="570d0-121">메서드의 코드에서이를 구성할 수도 있습니다 `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="570d0-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="570d0-122">JSON 기반 구성을 사용 하지 않는 경우 구성 시스템에서 다음 구성 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="570d0-123">구성 시스템에 대 한 설명서를 확인 하 여 중첩 된 구성 값을 지정 하는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="570d0-124">예를 들어 환경 변수를 사용 하는 경우 대신 두 개의 `_` 문자를 사용 `:` 합니다 (예: `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="570d0-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="570d0-125">`Debug`앱에 대 한 자세한 진단을 수집할 때 수준을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="570d0-126">`Trace`이 수준은 매우 낮은 수준의 진단을 생성 하며 앱에서 문제를 진단 하는 데 거의 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="570d0-127">서버 쪽 로그 액세스</span><span class="sxs-lookup"><span data-stu-id="570d0-127">Access server-side logs</span></span>

<span data-ttu-id="570d0-128">서버 쪽 로그에 액세스 하는 방법은를 실행 하는 환경에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="570d0-129">IIS 외부의 콘솔 앱으로</span><span class="sxs-lookup"><span data-stu-id="570d0-129">As a console app outside IIS</span></span>

<span data-ttu-id="570d0-130">콘솔 응용 프로그램에서 실행 하는 경우 [콘솔로 거](xref:fundamentals/logging/index#console) 를 기본적으로 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="570d0-131">로그가 콘솔에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="570d0-132">Visual Studio의 IIS Express 내에서</span><span class="sxs-lookup"><span data-stu-id="570d0-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="570d0-133">Visual Studio에서 로그 출력이 **출력** 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="570d0-134">**웹 서버 ASP.NET Core** 드롭다운 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="570d0-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="570d0-135">Azure App Service</span></span>

<span data-ttu-id="570d0-136">Azure App Service 포털의 **진단 로그** 섹션에서 **응용 프로그램 로깅 (파일 시스템)** 옵션을 사용 하도록 설정 하 고 **수준을** 로 구성 `Verbose` 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="570d0-137">로그 **스트리밍** 서비스와 App Service 파일 시스템의 로그에서 로그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="570d0-138">자세한 내용은 [Azure 로그 스트리밍](xref:fundamentals/logging/index#azure-log-streaming)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="570d0-139">기타 환경</span><span class="sxs-lookup"><span data-stu-id="570d0-139">Other environments</span></span>

<span data-ttu-id="570d0-140">앱을 다른 환경 (예: Docker, Kubernetes 또는 Windows 서비스)에 배포 하는 경우 <xref:fundamentals/logging/index> 환경에 적합 한 로깅 공급자를 구성 하는 방법에 대 한 자세한 내용은을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="570d0-141">JavaScript 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="570d0-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="570d0-142">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="570d0-143">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="570d0-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="570d0-144">JavaScript 클라이언트를 사용 하는 경우의 메서드를 사용 하 여 로깅 옵션을 구성할 수 있습니다 `configureLogging` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="570d0-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="570d0-145">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="570d0-146">다음 표에서는 JavaScript 클라이언트에서 사용할 수 있는 로그 수준을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="570d0-147">로그 수준을 이러한 값 중 하나로 설정 하면 해당 수준 및 테이블 위의 모든 수준에서 로깅을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="570d0-148">Level</span><span class="sxs-lookup"><span data-stu-id="570d0-148">Level</span></span> | <span data-ttu-id="570d0-149">Description</span><span class="sxs-lookup"><span data-stu-id="570d0-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="570d0-150">제목: ' ASP.NET Core SignalR ' 작성자: 설명: ' ASP.NET Core 앱에서 진단을 수집 하는 방법을 알아봅니다 SignalR . '</span><span class="sxs-lookup"><span data-stu-id="570d0-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="570d0-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="570d0-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="570d0-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="570d0-152">'Blazor'</span></span>
- <span data-ttu-id="570d0-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="570d0-153">'Identity'</span></span>
- <span data-ttu-id="570d0-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="570d0-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="570d0-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="570d0-155">'Razor'</span></span>
- <span data-ttu-id="570d0-156">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="570d0-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="570d0-157">제목: ' ASP.NET Core SignalR ' 작성자: 설명: ' ASP.NET Core 앱에서 진단을 수집 하는 방법을 알아봅니다 SignalR . '</span><span class="sxs-lookup"><span data-stu-id="570d0-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="570d0-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="570d0-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="570d0-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="570d0-159">'Blazor'</span></span>
- <span data-ttu-id="570d0-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="570d0-160">'Identity'</span></span>
- <span data-ttu-id="570d0-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="570d0-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="570d0-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="570d0-162">'Razor'</span></span>
- <span data-ttu-id="570d0-163">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="570d0-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="570d0-164">제목: ' ASP.NET Core SignalR ' 작성자: 설명: ' ASP.NET Core 앱에서 진단을 수집 하는 방법을 알아봅니다 SignalR . '</span><span class="sxs-lookup"><span data-stu-id="570d0-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="570d0-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="570d0-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="570d0-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="570d0-166">'Blazor'</span></span>
- <span data-ttu-id="570d0-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="570d0-167">'Identity'</span></span>
- <span data-ttu-id="570d0-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="570d0-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="570d0-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="570d0-169">'Razor'</span></span>
- <span data-ttu-id="570d0-170">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="570d0-170">'SignalR' uid:</span></span> 

<span data-ttu-id="570d0-171">------ | | `None` | 메시지가 기록 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="570d0-172">| | `Critical` | 전체 앱에서 오류를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="570d0-173">| | `Error` | 현재 작업의 실패를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="570d0-174">| | `Warning` | 심각 하지 않은 문제를 나타내는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="570d0-175">| | `Information` | 정보 메시지.</span><span class="sxs-lookup"><span data-stu-id="570d0-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="570d0-176">| | `Debug` | 디버깅에 유용한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="570d0-177">| | `Trace` | 특정 문제를 진단 하기 위해 설계 된 매우 자세한 진단 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="570d0-178">자세한 정도를 구성 하면 로그가 브라우저 콘솔에 기록 되거나 NodeJS 앱의 표준 출력에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="570d0-179">로그를 사용자 지정 로깅 시스템으로 전송 하려는 경우 인터페이스를 구현 하는 JavaScript 개체를 제공할 수 있습니다 `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="570d0-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="570d0-180">구현 해야 하는 유일한 방법은 이벤트 `log` 의 수준 및 이벤트와 연결 된 메시지를 가져오는입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="570d0-181">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="570d0-182">.NET 클라이언트 로깅</span><span class="sxs-lookup"><span data-stu-id="570d0-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="570d0-183">클라이언트 쪽 로그에는 앱의 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="570d0-184">프로덕션 앱의 원시 로그를 GitHub와 같은 공용 포럼에 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="570d0-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="570d0-185">에서 메서드를 사용 하 여 .NET 클라이언트에서 로그를 가져올 수 있습니다 `ConfigureLogging` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="570d0-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="570d0-186">이는 및에서 메서드와 동일한 방식으로 작동 합니다 `ConfigureLogging` `WebHostBuilder` `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="570d0-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="570d0-187">ASP.NET Core에서 사용 하는 것과 동일한 로깅 공급자를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="570d0-188">그러나 개별 로깅 공급자에 대 한 NuGet 패키지를 수동으로 설치 하 고 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="570d0-189">콘솔 로깅</span><span class="sxs-lookup"><span data-stu-id="570d0-189">Console logging</span></span>

<span data-ttu-id="570d0-190">콘솔 로깅을 사용 하도록 설정 하려면 [Microsoft 확장명이 console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="570d0-191">그런 다음 메서드를 사용 `AddConsole` 하 여 콘솔로 거를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="570d0-192">디버그 출력 창 로깅</span><span class="sxs-lookup"><span data-stu-id="570d0-192">Debug output window logging</span></span>

<span data-ttu-id="570d0-193">Visual Studio의 **출력** 창으로 이동 하도록 로그를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="570d0-194">[Microsoft Extensions. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 패키지를 설치 하 고 메서드를 사용 합니다 `AddDebug` .</span><span class="sxs-lookup"><span data-stu-id="570d0-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="570d0-195">기타 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="570d0-195">Other logging providers</span></span>

SignalR<span data-ttu-id="570d0-196">는 Serilog, Seq, NLog 또는와 통합 되는 기타 로깅 시스템과 같은 기타 로깅 공급자를 지원 `Microsoft.Extensions.Logging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="570d0-197">로깅 시스템에서를 제공 하는 경우 `ILoggerProvider` 다음을 사용 하 여 등록할 수 있습니다 `AddProvider` .</span><span class="sxs-lookup"><span data-stu-id="570d0-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="570d0-198">컨트롤 자세한 정도</span><span class="sxs-lookup"><span data-stu-id="570d0-198">Control verbosity</span></span>

<span data-ttu-id="570d0-199">앱의 다른 위치에서 로깅하는 경우 기본 수준을로 변경 하는 것이 `Debug` 너무 자세한 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="570d0-200">필터를 사용 하 여 로그의 로깅 수준을 구성할 수 있습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="570d0-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="570d0-201">이 작업은 서버에서와 거의 동일한 방식으로 코드에서 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="570d0-202">네트워크 추적</span><span class="sxs-lookup"><span data-stu-id="570d0-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="570d0-203">네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="570d0-204">프로덕션 앱에서 GitHub와 같은 공용 포럼에 원시 네트워크 추적을 게시 **하지 마세요** .</span><span class="sxs-lookup"><span data-stu-id="570d0-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="570d0-205">문제가 발생 하는 경우 네트워크 추적이 많은 유용한 정보를 제공 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="570d0-206">문제 추적기에서 문제를 해결 하려는 경우 특히 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="570d0-207">Fiddler를 사용 하 여 네트워크 추적 수집 (기본 설정 옵션)</span><span class="sxs-lookup"><span data-stu-id="570d0-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="570d0-208">이 메서드는 모든 앱에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-208">This method works for all apps.</span></span>

<span data-ttu-id="570d0-209">Fiddler는 HTTP 추적을 수집 하는 매우 강력한 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="570d0-210">[Telerik.com/fiddler](https://www.telerik.com/fiddler)에서 설치 하 고 시작 하 여 앱을 실행 하 고 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="570d0-211">Fiddler는 Windows에서 사용할 수 있으며 macOS 및 Linux에 대 한 베타 버전이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="570d0-212">HTTPS를 사용 하 여 연결 하는 경우 Fiddler에서 HTTPS 트래픽을 해독할 수 있는지 확인 하는 몇 가지 추가 단계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="570d0-213">자세한 내용은 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="570d0-214">추적을 수집한 후에는 **File**  >  **Save**  >  메뉴 모음에서 파일**모든 세션** 저장을 선택 하 여 추적을 내보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler에서 모든 세션 내보내기](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="570d0-216">Tcpdump를 사용 하 여 네트워크 추적 수집 (macOS 및 Linux에만 해당)</span><span class="sxs-lookup"><span data-stu-id="570d0-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="570d0-217">이 메서드는 모든 앱에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-217">This method works for all apps.</span></span>

<span data-ttu-id="570d0-218">명령 셸에서 다음 명령을 실행 하 여 tcpdump를 사용 하 여 원시 TCP 추적을 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="570d0-219">`root` `sudo` 사용 권한 오류가 발생 하는 경우 명령에 접두사를 사용 하거나 접두사로 사용 해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="570d0-220">을 `[interface]` 캡처할 네트워크 인터페이스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="570d0-221">일반적으로이는 `/dev/eth0` (표준 이더넷 인터페이스의 경우) 또는 `/dev/lo0` (localhost 트래픽용)와 같은 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="570d0-222">자세한 내용은 `tcpdump` 호스트 시스템의 man 페이지를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="570d0-223">브라우저에서 네트워크 추적 수집</span><span class="sxs-lookup"><span data-stu-id="570d0-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="570d0-224">이 메서드는 브라우저 기반 앱 에서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="570d0-225">대부분의 브라우저 개발자 도구 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="570d0-226">그러나 이러한 추적에는 WebSocket 및 서버에서 보낸 이벤트 메시지가 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="570d0-227">이러한 전송을 사용 하는 경우 Fiddler 또는 TcpDump와 같은 도구 (아래 설명 참조)를 사용 하는 것이 더 나은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="570d0-228">Microsoft Edge 및 Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="570d0-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="570d0-229">에 대 한 지침은 Edge 및 Internet Explorer 모두에 대해 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="570d0-230">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="570d0-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="570d0-231">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-231">Click the Network Tab</span></span>
3. <span data-ttu-id="570d0-232">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="570d0-233">도구 모음에서 저장 아이콘을 클릭 하 여 추적을 "HAR" 파일로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge Dev Tools 네트워크 탭의 저장 아이콘](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="570d0-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="570d0-235">Google Chrome</span></span>

1. <span data-ttu-id="570d0-236">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="570d0-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="570d0-237">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-237">Click the Network Tab</span></span>
3. <span data-ttu-id="570d0-238">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="570d0-239">요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "콘텐츠를 사용 하 여 HAR로 저장"을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome Dev Tools 네트워크 탭의 "콘텐츠를 사용 하 여 HAR로 저장" 옵션](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="570d0-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="570d0-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="570d0-242">F12 키를 눌러 개발자 도구 열기</span><span class="sxs-lookup"><span data-stu-id="570d0-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="570d0-243">네트워크 탭을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-243">Click the Network Tab</span></span>
3. <span data-ttu-id="570d0-244">페이지를 새로 고치고 (필요한 경우) 문제를 재현 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="570d0-245">요청 목록에서 아무 곳 이나 마우스 오른쪽 단추로 클릭 하 고 "모두 HAR로 저장"을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools 네트워크 탭에서 "모든 것을 HAR로 저장" 옵션](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="570d0-247">진단 파일을 GitHub 문제에 연결</span><span class="sxs-lookup"><span data-stu-id="570d0-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="570d0-248">확장을 포함 하 고 문제에 끌어서 놓으면 진단 파일의 이름을 바꿔서 GitHub 문제에 연결할 수 있습니다 `.txt` .</span><span class="sxs-lookup"><span data-stu-id="570d0-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="570d0-249">GitHub 문제에 로그 파일 또는 네트워크 추적의 내용을 붙여 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="570d0-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="570d0-250">이러한 로그와 추적은 매우 클 수 있으며 GitHub는 일반적으로이를 자릅니다.</span><span class="sxs-lookup"><span data-stu-id="570d0-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub 문제로 로그 파일 끌기](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="570d0-252">추가 자료</span><span class="sxs-lookup"><span data-stu-id="570d0-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
