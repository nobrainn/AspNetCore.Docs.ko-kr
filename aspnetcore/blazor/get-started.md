---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769457"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="a929d-103">ASP.NET Core Blazor 시작</span><span class="sxs-lookup"><span data-stu-id="a929d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="a929d-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a929d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a929d-105">Blazor를 시작하려면 아래에서 원하는 도구의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="a929d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a929d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a929d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a929d-107">Blazor 서버 앱을 만들려면 **ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a929d-108">Blazor 서버 앱과 Blazor WebAssembly 앱을 만들려면 **ASP.NET 및 웹 개발** 워크로드가 있는 최신 미리 보기 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a929d-109">두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  </span><span class="sxs-lookup"><span data-stu-id="a929d-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a929d-110">다음 명령을 실행하여 Blazor WebAssembly 미리 보기 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="a929d-111">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-111">Create a new project.</span></span>

1. <span data-ttu-id="a929d-112">**Blazor 앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-112">Select **Blazor App**.</span></span> <span data-ttu-id="a929d-113">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-113">Select **Next**.</span></span>

1. <span data-ttu-id="a929d-114">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a929d-115">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a929d-116">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-116">Select **Create**.</span></span>

1. <span data-ttu-id="a929d-117">Blazor WebAssembly 환경의 경우(Visual Studio 16.6 미리 보기 2 이상) **Blazor WebAssembly 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a929d-118">Blazor 서버 환경의 경우(Visual Studio 16.4 이상) **Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a929d-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-119">Select **Create**.</span></span>

1. <span data-ttu-id="a929d-120"><kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a929d-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a929d-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a929d-122">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="a929d-123">필요에 따라 다음 명령을 실행하여 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 미리 보기 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="a929d-124">두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  </span><span class="sxs-lookup"><span data-stu-id="a929d-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a929d-125">3\.2 미리 보기 Blazor WebAssembly 템플릿을 사용하려면 [.NET Core SDK 버전 3.1.201 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)이 **필요**합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="a929d-126">명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="a929d-127">[Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="a929d-128">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="a929d-129">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a929d-130">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="a929d-131">Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a929d-132">IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a929d-133">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-133">Select **Yes**.</span></span>

1. <span data-ttu-id="a929d-134">Blazor 서버를 사용하는 경우 Visual Studio Code 디버거를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="a929d-135">Blazor WebAssembly에서 **.NET Core 시작(Blazor 독립 실행형)** 시작 구성을 사용하여 앱을 시작한 다음 **Chrome의 .NET Core Debug Blazor Web Assembly** 시작 구성(Chrome 필요)을 사용하여 브라우저를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="a929d-136">자세한 내용은 <xref:blazor/debug#visual-studio-code>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a929d-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="a929d-137">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a929d-138">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a929d-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a929d-139">Mac용 Visual Studio에서는 Blazor 서버가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="a929d-140">현재 Blazor WebAssembly는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="a929d-141">macOS에서 Blazor WebAssembly 앱을 만들려면 **.NET Core CLI** 탭의 지침을 따르세요. 두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  </span><span class="sxs-lookup"><span data-stu-id="a929d-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a929d-142">[Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a929d-143">**파일** > **새 솔루션**을 선택하거나 **시작 창**에서 **새** 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a929d-144">사이드바에서 **.NET Core** > **앱**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="a929d-145">**Blazor 서버 앱** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="a929d-146">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-146">Select **Next**.</span></span>

1. <span data-ttu-id="a929d-147">다음 구성을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="a929d-148">**대상 프레임워크**가 **.NET Core 3.1**로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="a929d-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="a929d-149">**인증**이 **인증 안 함**으로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="a929d-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="a929d-150">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-150">Select **Next**.</span></span>

1. <span data-ttu-id="a929d-151">**프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a929d-152">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-152">Select **Create**.</span></span>

1. <span data-ttu-id="a929d-153">**실행** > **디버깅하지 않고 시작**을 선택하여 ‘디버거 없이’ 앱을 실행합니다. </span><span class="sxs-lookup"><span data-stu-id="a929d-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a929d-154">지금은 디버깅이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="a929d-155">개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a929d-156">인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a929d-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a929d-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="a929d-158">[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="a929d-159">필요에 따라 다음 명령을 실행하여 Blazor WebAssembly 미리 보기 템플릿을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="a929d-160">두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  </span><span class="sxs-lookup"><span data-stu-id="a929d-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a929d-161">3\.2 미리 보기 Blazor WebAssembly 템플릿을 사용하려면 [.NET Core SDK 버전 3.1.201 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)이 **필요**합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="a929d-162">명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="a929d-163">Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a929d-164">Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="a929d-165">브라우저에서 `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="a929d-166">사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a929d-167">Home</span><span class="sxs-lookup"><span data-stu-id="a929d-167">Home</span></span>
* <span data-ttu-id="a929d-168">카운터</span><span class="sxs-lookup"><span data-stu-id="a929d-168">Counter</span></span>
* <span data-ttu-id="a929d-169">데이터 가져오기</span><span class="sxs-lookup"><span data-stu-id="a929d-169">Fetch data</span></span>

<span data-ttu-id="a929d-170">Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a929d-171">웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="a929d-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a929d-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="a929d-173">맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="a929d-174">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a929d-175">**Click me** 단추를 선택할 때마다 다음 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a929d-176">`onclick` 이벤트가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a929d-177">`IncrementCount` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a929d-178">`currentCount`가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a929d-179">구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-179">The component is rendered again.</span></span>

<span data-ttu-id="a929d-180">런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a929d-181">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="a929d-182">예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="a929d-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a929d-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a929d-184">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-184">Run the app.</span></span> <span data-ttu-id="a929d-185">홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="a929d-186">구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용하여 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="a929d-187">`Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="a929d-188">`[Parameter]` 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a929d-189">`currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a929d-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a929d-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="a929d-191">특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a929d-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a929d-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="a929d-193">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-193">Run the app.</span></span> <span data-ttu-id="a929d-194">`Index` 구성 요소에는 **Click me** 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a929d-195">`/counter`의 `Counter` 구성 요소(*Counter.razor*)는 계속 1씩 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="a929d-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a929d-196">다음 단계</span><span class="sxs-lookup"><span data-stu-id="a929d-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="a929d-197">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a929d-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
