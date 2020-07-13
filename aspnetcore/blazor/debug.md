---
title: ASP.NET Core Blazor WebAssembly 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: c48eb19c5a1759aace112e2afb1637c649173a3d
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059905"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="d097e-103">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="d097e-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="d097e-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor WebAssembly<span data-ttu-id="d097e-105"> 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-105"> apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="d097e-106">또는 Visual Studio나 Visual Studio Code를 사용하여 앱을 디버그할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="d097e-107">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-107">Available scenarios include:</span></span>

* <span data-ttu-id="d097e-108">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="d097e-109">Visual Studio 및 Visual Studio Code에서 디버깅 지원으로 앱을 실행합니다(<kbd>F5</kbd> 키 지원).</span><span class="sxs-lookup"><span data-stu-id="d097e-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="d097e-110">코드를 한 단계씩 실행합니다(<kbd>F10</kbd> 키).</span><span class="sxs-lookup"><span data-stu-id="d097e-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="d097e-111">브라우저에서 <kbd>F8</kbd> 키를 사용하여 또는 Visual Studio나 Visual Studio Code에서 <kbd>F5</kbd> 키를 사용하여 코드 실행을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="d097e-112">‘로컬’ 표시에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="d097e-113">JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="d097e-114">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-114">For now, you *can't*:</span></span>

* <span data-ttu-id="d097e-115">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="d097e-116">앱 시작 중에 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="d097e-117">향후 릴리스에서 계속해서 디버깅 환경을 개선하기 위해 노력하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d097e-118">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d097e-118">Prerequisites</span></span>

<span data-ttu-id="d097e-119">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="d097e-120">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="d097e-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="d097e-121">Google Chrome(버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="d097e-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="d097e-122">Visual Studio 및 Visual Studio Code의 디버깅 사용 설정</span><span class="sxs-lookup"><span data-stu-id="d097e-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="d097e-123">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="d097e-124">업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="d097e-125">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="d097e-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="d097e-126">Blazor WebAssembly 앱이라는 것을 검색하도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="d097e-127">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="d097e-128">시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="d097e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d097e-129">Visual Studio</span></span>

<span data-ttu-id="d097e-130">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면:</span><span class="sxs-lookup"><span data-stu-id="d097e-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="d097e-131">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="d097e-132"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="d097e-133">`IncrementCount` 메서드의 `Pages/Counter.razor`에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="d097e-134">**`Counter`** 탭으로 이동하여 중단점을 적중시키도록 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![디버그 카운터](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="d097e-136">로컬 창에서 `currentCount` 필드의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![로컬 보기](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="d097e-138"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="d097e-139">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="d097e-140"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="d097e-141">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="d097e-142">**`Fetch Data`** 탭으로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![데이터 페치 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="d097e-144"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![서버 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="d097e-146"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="d097e-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d097e-147">Visual Studio Code</span></span>

<span data-ttu-id="d097e-148">Blazor 앱 개발을 위한 Visual Studio Code 설치에 대한 자세한 내용은 <xref:blazor/tooling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d097e-148">For information on installing Visual Studio Code for Blazor app development, see <xref:blazor/tooling>.</span></span>

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="d097e-149">독립 실행형 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="d097e-150">VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="d097e-151">디버깅을 사용하기 위해 추가 설정이 필요하다는 다음 알림이 표시되면</span><span class="sxs-lookup"><span data-stu-id="d097e-151">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="d097e-152">올바른 확장을 설치했는지 확인하고</span><span class="sxs-lookup"><span data-stu-id="d097e-152">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="d097e-153">JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-153">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="d097e-154">윈도우를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-154">Reload the window.</span></span>

   ![추가 설정 필요](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="d097e-156"><kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-156">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="d097e-157">메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-157">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![사용 가능한 디버그 옵션 목록](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="d097e-159">독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-159">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="d097e-160">`Counter` 구성 요소의 `IncrementCount` 메서드에 중단점을 설정하고 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-160">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code의 카운터 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="d097e-162">호스트된 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-162">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="d097e-163">VS Code에서 호스트된 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-163">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="d097e-164">프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-164">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="d097e-165">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-165">Select **Yes**.</span></span>

   ![필요한 자산 추가](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="d097e-167">선택 창에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-167">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="d097e-168">`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-168">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="d097e-169">기존 디버깅 세션에 연결</span><span class="sxs-lookup"><span data-stu-id="d097e-169">Attach to an existing debugging session</span></span>

<span data-ttu-id="d097e-170">실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-170">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="d097e-171">독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-171">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="d097e-172">전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-172">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="d097e-173">구성 옵션 시작</span><span class="sxs-lookup"><span data-stu-id="d097e-173">Launch configuration options</span></span>

<span data-ttu-id="d097e-174">`blazorwasm` 디버그 형식에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-174">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="d097e-175">옵션</span><span class="sxs-lookup"><span data-stu-id="d097e-175">Option</span></span>    | <span data-ttu-id="d097e-176">설명</span><span class="sxs-lookup"><span data-stu-id="d097e-176">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="d097e-177">`launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-177">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="d097e-178">디버깅할 때 브라우저에서 열 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-178">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="d097e-179">기본값은 `https://localhost:5001`입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-179">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="d097e-180">디버깅 세션을 시작하는 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-180">The browser to launch for the debugging session.</span></span> <span data-ttu-id="d097e-181">`edge` 또는 `chrome`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-181">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="d097e-182">기본값은 `chrome`입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-182">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="d097e-183">JS 디버거에서 로그를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-183">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="d097e-184">로그를 생성하려면 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-184">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="d097e-185">호스트된 Blazor WebAssembly 앱을 시작하고 디버그하는 경우 `true`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-185">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="d097e-186">웹 서버의 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-186">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="d097e-187">하위 경로에서 앱이 제공되는 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-187">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="d097e-188">디버깅 세션이 연결되기를 기다릴 밀리초 수입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-188">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="d097e-189">기본값은 30,000 밀리초(30초)입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-189">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="d097e-190">호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-190">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="d097e-191">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-191">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="d097e-192">앱을 시작할 작업 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-192">The working directory to launch the app under.</span></span> <span data-ttu-id="d097e-193">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-193">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="d097e-194">시작된 프로세스에 제공할 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-194">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="d097e-195">`hosted`가 `true`로 설정된 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-195">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="d097e-196">시작 구성 예제</span><span class="sxs-lookup"><span data-stu-id="d097e-196">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="d097e-197">독립 실행형 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-197">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="d097e-198">지정된 URL에서 실행 중인 앱에 연결</span><span class="sxs-lookup"><span data-stu-id="d097e-198">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="d097e-199">호스트된 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-199">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="d097e-200">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="d097e-200">Debug in the browser</span></span>

1. <span data-ttu-id="d097e-201">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-201">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="d097e-202">브라우저를 시작하고 앱의 URL(예: `https://localhost:5001`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-202">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="d097e-203">브라우저에서 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 눌러 원격 디버깅을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-203">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="d097e-204">브라우저는 원격 디버깅을 사용하도록 설정하고 실행해야 합니다(기본값 아님).</span><span class="sxs-lookup"><span data-stu-id="d097e-204">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="d097e-205">원격 디버깅을 사용하지 않도록 설정하면 디버깅 포트가 열려 있는 브라우저를 시작하라는 지침과 함께 **디버깅 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-205">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="d097e-206">브라우저에 대한 지침을 다릅니다. 그러면 새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-206">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="d097e-207">이전 브라우저 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-207">Close the previous browser window.</span></span>

1. <span data-ttu-id="d097e-208">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 디버깅 바로 가기 키(<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>)를 누르면 새 디버거 탭이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-208">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="d097e-209">잠시 후 **소스** 탭에는 `file://` 노드 내에 있는 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-209">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="d097e-210">구성 요소 코드(`.razor` 파일) 및 C# 코드 파일(`.cs`)에서 사용자가 설정하는 중단점은 코드가 실행될 때 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-210">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="d097e-211">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-211">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="d097e-212">에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-212"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="d097e-213">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-213">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="d097e-214">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-214">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="d097e-215">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="d097e-215">Browser source maps</span></span>

<span data-ttu-id="d097e-216">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-216">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="d097e-217">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-217">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="d097e-218">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-218">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d097e-219">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d097e-219">Troubleshoot</span></span>

<span data-ttu-id="d097e-220">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-220">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="d097e-221">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-221">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="d097e-222">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-222">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="d097e-223">ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-223">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="d097e-224">자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d097e-224">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="d097e-225">Visual Studio를 사용하려면 **도구** > **옵션** > **디버깅** > **일반**에서 **ASP.NET에 대해 JavaScript 디버깅 사용(Chrome, Edge 및 IE)** 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-225">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="d097e-226">이것은 Visual Studio의 기본 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-226">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="d097e-227">디버깅이 작동하지 않는 경우 옵션이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d097e-227">If debugging isn't working, confirm that the option is selected.</span></span>
