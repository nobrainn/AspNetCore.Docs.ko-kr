---
title: ASP.NET Core Blazor WebAssembly 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 75db5d5e69cb200ebf3bd1dc1e0afed0300214cc
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242773"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="a87bd-103">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="a87bd-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="a87bd-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="a87bd-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="a87bd-105"> WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="a87bd-106">또는 Visual Studio나 Visual Studio Code를 사용하여 앱을 디버그할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="a87bd-107">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-107">Available scenarios include:</span></span>

* <span data-ttu-id="a87bd-108">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="a87bd-109">Visual Studio 및 Visual Studio Code에서 디버깅 지원으로 앱을 실행합니다(<kbd>F5</kbd> 키 지원).</span><span class="sxs-lookup"><span data-stu-id="a87bd-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="a87bd-110">코드를 한 단계씩 실행합니다(<kbd>F10</kbd> 키).</span><span class="sxs-lookup"><span data-stu-id="a87bd-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="a87bd-111">브라우저에서 <kbd>F8</kbd> 키를 사용하여 또는 Visual Studio나 Visual Studio Code에서 <kbd>F5</kbd> 키를 사용하여 코드 실행을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="a87bd-112">‘로컬’ 표시에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="a87bd-113">JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="a87bd-114">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-114">For now, you *can't*:</span></span>

* <span data-ttu-id="a87bd-115">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="a87bd-116">앱 시작 중에 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="a87bd-117">향후 릴리스에서 계속해서 디버깅 환경을 개선하기 위해 노력하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a87bd-118">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="a87bd-118">Prerequisites</span></span>

<span data-ttu-id="a87bd-119">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="a87bd-120">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="a87bd-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="a87bd-121">Google Chrome(버전 70 이상)</span><span class="sxs-lookup"><span data-stu-id="a87bd-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="a87bd-122">Visual Studio 및 Visual Studio Code의 디버깅 사용 설정</span><span class="sxs-lookup"><span data-stu-id="a87bd-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="a87bd-123">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="a87bd-124">업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="a87bd-125">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="a87bd-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="a87bd-126">앱이 Blazor WebAssembly 앱인지를 감지할 수 있도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="a87bd-127">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="a87bd-128">시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="a87bd-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a87bd-129">Visual Studio</span></span>

<span data-ttu-id="a87bd-130">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면</span><span class="sxs-lookup"><span data-stu-id="a87bd-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="a87bd-131">ASP.NET Core가 호스트한 새 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="a87bd-132"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="a87bd-133">`IncrementCount` 메서드의 `Pages/Counter.razor`에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="a87bd-134">**`Counter`** 탭으로 이동하여 중단점을 적중시키도록 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![디버그 카운터](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="a87bd-136">로컬 창에서 `currentCount` 필드의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![로컬 보기](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="a87bd-138"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="a87bd-139">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="a87bd-140"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="a87bd-141">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="a87bd-142">**`Fetch Data`** 탭으로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![데이터 페치 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="a87bd-144"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![서버 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="a87bd-146"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="a87bd-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a87bd-147">Visual Studio Code</span></span>

<span data-ttu-id="a87bd-148">Visual Studio Code에서 Blazor WebAssembly 앱을 디버그하려면</span><span class="sxs-lookup"><span data-stu-id="a87bd-148">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="a87bd-149">[C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-149">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![확장](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS 미리 보기 디버거](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="a87bd-152">디버그 독립 실행형 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a87bd-152">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="a87bd-153">VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-153">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="a87bd-154">디버깅을 사용하기 위해 추가 설정이 필요하다는 다음 알림이 표시되면</span><span class="sxs-lookup"><span data-stu-id="a87bd-154">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="a87bd-155">올바른 확장을 설치했는지 확인하고</span><span class="sxs-lookup"><span data-stu-id="a87bd-155">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="a87bd-156">JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-156">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="a87bd-157">윈도우를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-157">Reload the window.</span></span>

   ![추가 설정 필요](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="a87bd-159"><kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-159">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="a87bd-160">메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-160">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![사용 가능한 디버그 옵션 목록](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="a87bd-162">독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-162">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="a87bd-163">`Counter` 구성 요소의 `IncrementCount` 메서드에 중단점을 설정하고 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-163">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code의 카운터 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="a87bd-165">호스트된 Blazor WebAssembly를 디버그합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-165">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="a87bd-166">VS Code에서 호스트된 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-166">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="a87bd-167">프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-167">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="a87bd-168">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-168">Select **Yes**.</span></span>

   ![필요한 자산 추가](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="a87bd-170">선택 창에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-170">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="a87bd-171">`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-171">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="a87bd-172">기존 디버깅 세션에 연결</span><span class="sxs-lookup"><span data-stu-id="a87bd-172">Attach to an existing debugging session</span></span>

<span data-ttu-id="a87bd-173">실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-173">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="a87bd-174">독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-174">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="a87bd-175">전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-175">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="a87bd-176">구성 옵션 시작</span><span class="sxs-lookup"><span data-stu-id="a87bd-176">Launch configuration options</span></span>

<span data-ttu-id="a87bd-177">`blazorwasm` 디버그 형식에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-177">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="a87bd-178">옵션</span><span class="sxs-lookup"><span data-stu-id="a87bd-178">Option</span></span>    | <span data-ttu-id="a87bd-179">설명</span><span class="sxs-lookup"><span data-stu-id="a87bd-179">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="a87bd-180">`launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-180">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="a87bd-181">디버깅할 때 브라우저에서 열 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-181">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="a87bd-182">기본값은 `https://localhost:5001`입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-182">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="a87bd-183">디버깅 세션을 시작하는 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-183">The browser to launch for the debugging session.</span></span> <span data-ttu-id="a87bd-184">`edge` 또는 `chrome`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-184">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="a87bd-185">기본값은 `chrome`입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-185">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="a87bd-186">JS 디버거에서 로그를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-186">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="a87bd-187">로그를 생성하려면 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-187">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="a87bd-188">호스트된 Blazor WebAssembly 앱을 시작하고 디버깅하는 경우 `true`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-188">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="a87bd-189">웹 서버의 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-189">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="a87bd-190">하위 경로에서 앱이 제공되는 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-190">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="a87bd-191">디버깅 세션이 연결되기를 기다릴 밀리초 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-191">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="a87bd-192">기본값은 30,000 밀리초(30초)입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-192">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="a87bd-193">호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-193">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="a87bd-194">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-194">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="a87bd-195">앱을 시작할 작업 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-195">The working directory to launch the app under.</span></span> <span data-ttu-id="a87bd-196">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-196">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="a87bd-197">시작된 프로세스에 제공할 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-197">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="a87bd-198">`hosted`가 `true`로 설정된 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-198">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="a87bd-199">시작 구성 예제</span><span class="sxs-lookup"><span data-stu-id="a87bd-199">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="a87bd-200">독립 실행형 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="a87bd-200">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="a87bd-201">지정된 URL에서 실행 중인 앱에 연결</span><span class="sxs-lookup"><span data-stu-id="a87bd-201">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="a87bd-202">호스트된 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="a87bd-202">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="a87bd-203">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="a87bd-203">Debug in the browser</span></span>

1. <span data-ttu-id="a87bd-204">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-204">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="a87bd-205"><kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-205">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="a87bd-206">브라우저는 원격 디버깅이 설정된 상태로 실행되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-206">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="a87bd-207">원격 디버깅을 사용하지 않도록 설정한 경우, **디버그 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-207">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="a87bd-208">오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열려 있는 상태에서 브라우저를 실행하는 작업에 대한 지침이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-208">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="a87bd-209">‘모든 브라우저 인스턴스를 닫고’ 지침에 따라 브라우저를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-209">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="a87bd-210">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 디버깅 바로 가기 키를 누르면 새 디버거 탭이 열립니다. 잠시 후에 **소스** 탭에 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-210">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="a87bd-211">각 어셈블리를 펼쳐 디버깅에 사용할 수 있는 `.cs`/`.razor` 원본 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-211">Expand each assembly and find the `.cs`/`.razor` source files available for debugging.</span></span> <span data-ttu-id="a87bd-212">중단점을 설정하고 앱의 탭으로 다시 전환한 다음, 코드를 실행하면 중단점이 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-212">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="a87bd-213">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-213">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="a87bd-214">에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-214"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="a87bd-215">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-215">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="a87bd-216">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-216">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="a87bd-217">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="a87bd-217">Browser source maps</span></span>

<span data-ttu-id="a87bd-218">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-218">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="a87bd-219">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-219">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="a87bd-220">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-220">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a87bd-221">문제 해결</span><span class="sxs-lookup"><span data-stu-id="a87bd-221">Troubleshoot</span></span>

<span data-ttu-id="a87bd-222">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-222">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="a87bd-223">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-223">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="a87bd-224">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-224">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="a87bd-225">ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a87bd-225">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="a87bd-226">자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a87bd-226">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
