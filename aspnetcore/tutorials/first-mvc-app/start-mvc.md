---
title: ASP.NET Core MVC 시작
author: rick-anderson
description: ASP.NET Core MVC를 시작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 01321d68defafbe79371250669f921307bcfdba6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407046"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d2e64-103">ASP.NET Core MVC 시작</span><span class="sxs-lookup"><span data-stu-id="d2e64-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d2e64-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d2e64-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d2e64-105">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d2e64-106">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="d2e64-107">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2e64-108">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d2e64-108">Create a web app.</span></span>
> * <span data-ttu-id="d2e64-109">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="d2e64-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d2e64-110">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="d2e64-110">Work with a database.</span></span>
> * <span data-ttu-id="d2e64-111">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="d2e64-111">Add search and validation.</span></span>

<span data-ttu-id="d2e64-112">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d2e64-113">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d2e64-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-116">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d2e64-117">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d2e64-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2e64-119">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d2e64-120">**ASP.NET Core 웹 응용 프로그램**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d2e64-122">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d2e64-123">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)

* <span data-ttu-id="d2e64-125">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d2e64-126">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="d2e64-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d2e64-127">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d2e64-128">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d2e64-129">이 프로젝트가 기본 시작 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2e64-131">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d2e64-132">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d2e64-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d2e64-133">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d2e64-134">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d2e64-135">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d2e64-136">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d2e64-137">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-137">Select **Yes**</span></span>

  * <span data-ttu-id="d2e64-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d2e64-139">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-140">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2e64-141">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-141">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d2e64-143">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d2e64-144">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d2e64-146">다음 구성을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="d2e64-147">**대상 프레임워크**가 **.NET Core 3.1**로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="d2e64-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="d2e64-148">**인증**이 **인증 안 함**으로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="d2e64-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="d2e64-149">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-149">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 선택](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="d2e64-151">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d2e64-153">앱 실행</span><span class="sxs-lookup"><span data-stu-id="d2e64-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d2e64-155">**Ctrl-F5**를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d2e64-156">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d2e64-157">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="d2e64-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-158">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2e64-159">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d2e64-160">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2e64-161">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d2e64-162">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d2e64-164">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d2e64-166">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-166">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2e64-169">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d2e64-170">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d2e64-171">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-172">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d2e64-173">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d2e64-174">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2e64-175">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-177">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d2e64-178">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d2e64-179">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d2e64-180">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-181">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2e64-182">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d2e64-183">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d2e64-184">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d2e64-185">다음 이미지는 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-185">The following image shows the app:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d2e64-187">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d2e64-188">다음</span><span class="sxs-lookup"><span data-stu-id="d2e64-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d2e64-189">이 자습서에서는 ASP.NET Core MVC 웹앱을 만들기 위한 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d2e64-190">앱은 동영상 제목의 데이터베이스를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="d2e64-191">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2e64-192">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d2e64-192">Create a web app.</span></span>
> * <span data-ttu-id="d2e64-193">모델 추가 및 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="d2e64-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d2e64-194">데이터베이스 작업</span><span class="sxs-lookup"><span data-stu-id="d2e64-194">Work with a database.</span></span>
> * <span data-ttu-id="d2e64-195">검색 및 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="d2e64-195">Add search and validation.</span></span>

<span data-ttu-id="d2e64-196">자습서를 마치고 나면 동영상 데이터를 관리하고 표시할 수 있는 앱을 만들게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d2e64-197">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d2e64-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-200">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="d2e64-201">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d2e64-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2e64-203">Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d2e64-204">**ASP.NET Core 웹 응용 프로그램**을 선택한 후, **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d2e64-206">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d2e64-207">코드를 복사할 때 네임스페이스가 일치하도록 프로젝트 이름을 **MvcMovie**로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](start-mvc/_static/config.png)


* <span data-ttu-id="d2e64-209">**웹 애플리케이션(Model-View-Controller)** 을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d2e64-210">새 프로젝트 대화 상자, 왼쪽 창의 .NET Core, ASP.NET Core 웹</span><span class="sxs-lookup"><span data-stu-id="d2e64-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="d2e64-211">Visual Studio는 방금 만든 MVC 프로젝트에 대해 기본 템플릿을 사용했습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d2e64-212">프로젝트 이름을 입력하고 몇 가지 옵션을 선택하여 바로 작동하는 앱을 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d2e64-213">다음은 기본 시작 프로젝트이며 여기서 시작하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2e64-215">이 자습서에서는 VS Code를 잘 알고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d2e64-216">자세한 내용은 [VS Code 시작](https://code.visualstudio.com/docs) 및 [Visual Studio Code 도움말](#visual-studio-code-help)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d2e64-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d2e64-217">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d2e64-218">프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d2e64-219">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d2e64-220">**Required assets to build and debug are missing from 'MvcMovie'.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d2e64-221">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-221">Select **Yes**</span></span>

  * <span data-ttu-id="d2e64-222">`dotnet new mvc -o MvcMovie`: *MvcMovie* 폴더에 새 ASP.NET Core MVC 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d2e64-223">`code -r MvcMovie`: Visual Studio Code에서 *MvcMovie.csproj* 프로젝트 파일을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-224">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2e64-225">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-225">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d2e64-227">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d2e64-228">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션(Model-View-Controller)**  > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="d2e64-229">**새 ASP.NET Core Web API 구성** 대화 상자에서 **.NET Core 2.2**라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 선택](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="d2e64-231">프로젝트 이름을 **MvcMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="d2e64-232">앱 실행</span><span class="sxs-lookup"><span data-stu-id="d2e64-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2e64-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d2e64-234">**Ctrl-F5**를 선택하여 비 디버그 모드에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d2e64-235">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d2e64-236">주소 표시줄에 `localhost:port#` 같은 주소 대신 `example.com`가 표시되는 점에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="d2e64-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-237">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2e64-238">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d2e64-239">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2e64-240">대부분의 개발자는 앱을 빠르게 시작하고 변경 내용을 확인하기 위해 비 디버그 모드를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d2e64-241">**디버그** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![디버그 메뉴](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d2e64-243">**IIS Express** 단추를 선택하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="d2e64-245">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2e64-246">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-246">This app doesn't track personal information.</span></span> <span data-ttu-id="d2e64-247">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="d2e64-249">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-249">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2e64-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2e64-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2e64-252">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d2e64-253">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `https://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d2e64-254">주소 표시줄에는 `localhost:port:5001`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-255">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d2e64-256">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d2e64-257">Ctrl+F5(비 디버그 모드)를 사용하여 앱을 시작하면 코드를 변경하고, 파일을 저장하고, 브라우저를 새로 고치고, 코드 변경 내용을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d2e64-258">대부분의 개발자는 페이지 및 보기 변경 내용을 새로 고치기 위해 디버그 이외 모드를 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="d2e64-259">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2e64-260">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-260">This app doesn't track personal information.</span></span> <span data-ttu-id="d2e64-261">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/privacy.png)

  <span data-ttu-id="d2e64-263">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-263">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2e64-265">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2e64-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d2e64-266">**실행** > **디버깅하지 않고 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d2e64-267">Mac용 Visual Studio에서 [Kestrel](xref:fundamentals/servers/index#kestrel) 서버를 시작하고, 브라우저를 실행하며, `http://localhost:port`로 이동합니다. 여기서 *port*는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d2e64-268">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d2e64-269">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d2e64-270">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d2e64-271">앱을 실행할 경우 다른 포트 번호가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d2e64-272">**실행** 메뉴 항목에서 앱을 디버그 또는 비 디버그 모드로 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="d2e64-273">**Accept**를 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d2e64-274">이 앱은 개인 정보를 추적하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-274">This app doesn't track personal information.</span></span> <span data-ttu-id="d2e64-275">템플릿 생성 코드는 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하는 데 도움이 되는 자산을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="d2e64-277">다음 이미지는 추적을 승인한 후의 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-277">The following image shows the app after accepting tracking:</span></span>

  ![홈 또는 인덱스 페이지](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d2e64-279">이 자습서의 다음 부분에서는 MVC에 대해 알아보고 코드 작성을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d2e64-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d2e64-280">다음</span><span class="sxs-lookup"><span data-stu-id="d2e64-280">Next</span></span>](adding-controller.md)

::: moniker-end
