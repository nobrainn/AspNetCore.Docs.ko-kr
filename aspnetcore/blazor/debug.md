---
<span data-ttu-id="d1438-101">title: 'ASP.NET Core Blazor WebAssembly 디버깅' author: description: 'Blazor 앱을 디버그하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="d1438-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="d1438-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d1438-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d1438-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d1438-103">'Blazor'</span></span>
- <span data-ttu-id="d1438-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d1438-104">'Identity'</span></span>
- <span data-ttu-id="d1438-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d1438-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d1438-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d1438-106">'Razor'</span></span>
- <span data-ttu-id="d1438-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="d1438-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="d1438-108">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="d1438-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="d1438-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="d1438-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="d1438-110"> WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="d1438-111">또는 Visual Studio나 Visual Studio Code를 사용하여 앱을 디버그할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="d1438-112">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-112">Available scenarios include:</span></span>

* <span data-ttu-id="d1438-113">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="d1438-114">Visual Studio 및 Visual Studio Code에서 디버깅 지원으로 앱을 실행합니다(<kbd>F5</kbd> 키 지원).</span><span class="sxs-lookup"><span data-stu-id="d1438-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="d1438-115">코드를 한 단계씩 실행합니다(<kbd>F10</kbd> 키).</span><span class="sxs-lookup"><span data-stu-id="d1438-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="d1438-116">브라우저에서 <kbd>F8</kbd> 키를 사용하여 또는 Visual Studio나 Visual Studio Code에서 <kbd>F5</kbd> 키를 사용하여 코드 실행을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="d1438-117">‘로컬’ 표시에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="d1438-118">JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="d1438-119">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-119">For now, you *can't*:</span></span>

* <span data-ttu-id="d1438-120">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="d1438-121">앱 시작 중에 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="d1438-122">향후 릴리스에서 계속해서 디버깅 환경을 개선하기 위해 노력하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d1438-123">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d1438-123">Prerequisites</span></span>

<span data-ttu-id="d1438-124">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="d1438-125">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="d1438-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="d1438-126">Google Chrome(버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="d1438-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="d1438-127">Visual Studio 및 Visual Studio Code의 디버깅 사용 설정</span><span class="sxs-lookup"><span data-stu-id="d1438-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="d1438-128">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 *launchSettings.json* 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="d1438-129">업데이트하면 *launchSettings.json* 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="d1438-130">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="d1438-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="d1438-131">앱이 Blazor WebAssembly 앱인지를 감지할 수 있도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="d1438-132">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="d1438-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1438-133">Visual Studio</span></span>

<span data-ttu-id="d1438-134">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면</span><span class="sxs-lookup"><span data-stu-id="d1438-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="d1438-135">ASP.NET Core가 호스트한 새 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="d1438-136"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="d1438-137">`IncrementCount` 메서드의 *Counter.razor*에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="d1438-138">**카운터** 탭으로 이동하여 중단점을 적중시키도록 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![디버그 카운터](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="d1438-140">로컬 창에서 `currentCount` 필드의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![로컬 보기](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="d1438-142"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="d1438-143">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="d1438-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 *FetchData.razor* 페이지에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="d1438-145">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="d1438-146">**데이터 페치** 탭으로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![데이터 페치 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="d1438-148"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![서버 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="d1438-150"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="d1438-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1438-151">Visual Studio Code</span></span>

<span data-ttu-id="d1438-152">Visual Studio Code에서 Blazor WebAssembly 앱을 디버그하려면</span><span class="sxs-lookup"><span data-stu-id="d1438-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="d1438-153">[C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![확장](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS 미리 보기 디버거](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="d1438-156">디버깅이 사용 설정된 상태에서 기존 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="d1438-157">디버깅을 사용하도록 설정하려면 추가 설정이 필요하다는 다음 알림이 표시되는 경우, 올바른 확장을 설치했고 JavaScript 미리 보기 디버깅을 사용하도록 설정했는지 확인한 다음에 창을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![추가 설정 필요](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="d1438-159">알림에서 빌드 및 디버깅을 위해 앱에 필요한 자산을 추가할지 묻습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="d1438-160">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-160">Select **Yes**:</span></span>

     ![필요한 자산 추가](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="d1438-162">디버거를 앱에서 시작하는 작업은 두 단계로 진행되는 프로세스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="d1438-163">1\.</span><span class="sxs-lookup"><span data-stu-id="d1438-163">1\.</span></span> <span data-ttu-id="d1438-164">**먼저** **.NET Core 시작(Blazor 독립 실행형)** 시작 구성을 사용하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="d1438-165">2\.</span><span class="sxs-lookup"><span data-stu-id="d1438-165">2\.</span></span> <span data-ttu-id="d1438-166">**앱이 시작된 후에** **Chrome의 .NET Core Debug Blazor 웹 어셈블리** 시작 구성을 사용하여 브라우저를 시작합니다(Chrome 필요).</span><span class="sxs-lookup"><span data-stu-id="d1438-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="d1438-167">Chrome 대신 Edge를 사용하려면 *.vscode/launch.json*에 있는 시작 구성의 `type`을 `pwa-chrome`에서 `pwa-msedge`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="d1438-168">`Counter` 구성 요소의 `IncrementCount` 메서드에 중단점을 설정하고 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code의 카운터 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="d1438-170">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="d1438-170">Debug in the browser</span></span>

1. <span data-ttu-id="d1438-171">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="d1438-172"><kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="d1438-173">브라우저는 원격 디버깅이 설정된 상태로 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="d1438-174">원격 디버깅을 사용하지 않도록 설정한 경우, **디버그 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="d1438-175">오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열려 있는 상태에서 브라우저를 실행하는 작업에 대한 지침이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="d1438-176">‘모든 브라우저 인스턴스를 닫고’ 지침에 따라 브라우저를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="d1438-177">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 디버깅 바로 가기 키를 누르면 새 디버거 탭이 열립니다. 잠시 후에 **소스** 탭에 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="d1438-178">각 어셈블리를 펼쳐 디버깅에 사용할 수 있는 *.cs*/ *.razor* 소스 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="d1438-179">중단점을 설정하고 앱의 탭으로 다시 전환한 다음, 코드를 실행하면 중단점이 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="d1438-180">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="d1438-181">에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="d1438-182">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="d1438-183">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="d1438-184">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="d1438-184">Browser source maps</span></span>

<span data-ttu-id="d1438-185">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="d1438-186">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="d1438-187">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d1438-188">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d1438-188">Troubleshoot</span></span>

<span data-ttu-id="d1438-189">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="d1438-190">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="d1438-191">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d1438-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
