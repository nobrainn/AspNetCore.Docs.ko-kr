---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
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
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243605"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="98b2d-103">ASP.NET Core Blazor 시작하기</span><span class="sxs-lookup"><span data-stu-id="98b2d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="98b2d-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98b2d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="98b2d-105">Blazor를 시작하려면 아래에서 원하는 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="98b2d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98b2d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98b2d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="98b2d-107">**ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="98b2d-108">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-108">Create a new project.</span></span>

1. <span data-ttu-id="98b2d-109">**Blazor 앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-109">Select **Blazor App**.</span></span> <span data-ttu-id="98b2d-110">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-110">Select **Next**.</span></span>

1. <span data-ttu-id="98b2d-111">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="98b2d-112">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="98b2d-113">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-113">Select **Create**.</span></span>

1. <span data-ttu-id="98b2d-114">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="98b2d-115">Blazor 서버 환경의 경우 **Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="98b2d-116">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-116">Select **Create**.</span></span>

   <span data-ttu-id="98b2d-117">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98b2d-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="98b2d-118"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="98b2d-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98b2d-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="98b2d-120">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)의 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="98b2d-121">이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="98b2d-122">최신 버전의 [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="98b2d-123">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="98b2d-124">VS Code UI를 사용하여 `debug.javascript.usePreview`를 `true`로 설정하려면 **파일** > **기본 설정** > **설정**을 열고 `debug javascript use preview`를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="98b2d-125">**Node.js 및 Chrome을 위한 새로운 미리 보기 JavaScript 디버거를 사용합니다.** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="98b2d-126">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="98b2d-127">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="98b2d-128">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98b2d-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="98b2d-129">Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="98b2d-130">IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="98b2d-131">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-131">Select **Yes**.</span></span>

1. <span data-ttu-id="98b2d-132"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="98b2d-133">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98b2d-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="98b2d-134">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="98b2d-135">**파일** > **새 솔루션**을 선택하거나 **시작 창**에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="98b2d-136">사이드바에서 **웹 및 콘솔** > **앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="98b2d-137">Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="98b2d-138">Blazor 서버 환경의 경우 **Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="98b2d-139">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-139">Select **Next**.</span></span>

   <span data-ttu-id="98b2d-140">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98b2d-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="98b2d-141">다음 구성을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="98b2d-142">**대상 프레임워크**가 **.NET Core 3.1**로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="98b2d-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="98b2d-143">**인증**이 **인증 안 함**으로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="98b2d-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="98b2d-144">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-144">Select **Next**.</span></span>

1. <span data-ttu-id="98b2d-145">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="98b2d-146">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-146">Select **Create**.</span></span>

1. <span data-ttu-id="98b2d-147">**실행** > **디버깅하지 않고 시작**을 선택하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="98b2d-148">**실행** > **디버깅 시작**으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="98b2d-149">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="98b2d-150">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="98b2d-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="98b2d-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="98b2d-152">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)의 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="98b2d-153">이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="98b2d-154">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="98b2d-155">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="98b2d-156">두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="98b2d-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="98b2d-157">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="98b2d-158">사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="98b2d-159">Home</span><span class="sxs-lookup"><span data-stu-id="98b2d-159">Home</span></span>
* <span data-ttu-id="98b2d-160">카운터</span><span class="sxs-lookup"><span data-stu-id="98b2d-160">Counter</span></span>
* <span data-ttu-id="98b2d-161">데이터 가져오기</span><span class="sxs-lookup"><span data-stu-id="98b2d-161">Fetch data</span></span>

<span data-ttu-id="98b2d-162">Counter 페이지에서 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="98b2d-163">웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="98b2d-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="98b2d-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="98b2d-165">맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="98b2d-166">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="98b2d-167">단추를 선택할 때마다 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-167">Each time the button is selected:</span></span>

* <span data-ttu-id="98b2d-168">`onclick` 이벤트가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="98b2d-169">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="98b2d-170">`currentCount`가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="98b2d-171">구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-171">The component is rendered again.</span></span>

<span data-ttu-id="98b2d-172">런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="98b2d-173">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="98b2d-174">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="98b2d-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="98b2d-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="98b2d-176">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-176">Run the app.</span></span> <span data-ttu-id="98b2d-177">홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="98b2d-178">구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components/index#child-content)를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="98b2d-179">`Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="98b2d-180">[`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="98b2d-181">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="98b2d-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="98b2d-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="98b2d-183">특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="98b2d-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="98b2d-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="98b2d-185">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-185">Run the app.</span></span> <span data-ttu-id="98b2d-186">`Index` 구성 요소에는 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="98b2d-187">`/counter`의 `Counter` 구성 요소(`Pages/Counter.razor`)는 계속 1씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="98b2d-188">다음 단계</span><span class="sxs-lookup"><span data-stu-id="98b2d-188">Next steps</span></span>

<span data-ttu-id="98b2d-189">Blazor 앱을 단계별로 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="98b2d-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="98b2d-190">추가 자료</span><span class="sxs-lookup"><span data-stu-id="98b2d-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
