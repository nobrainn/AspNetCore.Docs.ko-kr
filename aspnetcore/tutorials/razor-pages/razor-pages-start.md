---
title: '자습서: ASP.NET Core에서 Razor Pages 시작'
author: rick-anderson
description: 이 자습서 시리즈는 ASP.NET Core에서 Razor Pages를 사용하는 방법을 보여 줍니다. 모델을 만들고, Razor Pages에 대한 코드를 생성하고, Entity Framework Core 및 SQL Server를 데이터 액세스에 사용하고, 검색 기능을 추가하고, 입력 유효성 검사를 추가하고, 마이그레이션을 사용하여 모델을 업데이트하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 97e3f60480bc8e7e88c8361e5b13f02d98765d9e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405304"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="4ea36-104">자습서: ASP.NET Core에서 Razor Pages 시작</span><span class="sxs-lookup"><span data-stu-id="4ea36-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4ea36-105">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ea36-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="4ea36-106">ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명하는 자습서 시리즈 중 첫 번째입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4ea36-107">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4ea36-108">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ea36-109">Razor Pages 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4ea36-110">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-110">Run the app.</span></span>
> * <span data-ttu-id="4ea36-111">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-111">Examine the project files.</span></span>

<span data-ttu-id="4ea36-112">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4ea36-114">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4ea36-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ea36-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ea36-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ea36-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ea36-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4ea36-118">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4ea36-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ea36-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ea36-120">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4ea36-121">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="4ea36-122">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="4ea36-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="4ea36-123">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4ea36-124">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="4ea36-125">![새 ASP.NET Core 웹 애플리케이션](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="4ea36-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="4ea36-126">드롭다운에서 **ASP.NET Core 3.1**을 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="4ea36-128">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-128">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ea36-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ea36-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ea36-131">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4ea36-132">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4ea36-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4ea36-134">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4ea36-135">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4ea36-136">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4ea36-137">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-137">Select **Yes**.</span></span>

  <span data-ttu-id="4ea36-138">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ea36-139">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ea36-140">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-140">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4ea36-142">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="4ea36-143">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS 웹앱 템플릿 선택](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="4ea36-145">다음 구성을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="4ea36-146">**대상 프레임워크**가 **.NET Core 3.1**로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="4ea36-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="4ea36-147">**인증**이 **인증 안 함**으로 설정되어 있는지</span><span class="sxs-lookup"><span data-stu-id="4ea36-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="4ea36-148">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-148">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 선택](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="4ea36-150">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 프로젝트 이름 지정](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4ea36-152">앱 실행</span><span class="sxs-lookup"><span data-stu-id="4ea36-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="4ea36-153">프로젝트 파일 검토</span><span class="sxs-lookup"><span data-stu-id="4ea36-153">Examine the project files</span></span>

<span data-ttu-id="4ea36-154">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4ea36-155">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="4ea36-155">Pages folder</span></span>

<span data-ttu-id="4ea36-156">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4ea36-157">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4ea36-158">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="4ea36-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4ea36-159">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="4ea36-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4ea36-160">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4ea36-161">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4ea36-162">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4ea36-163">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4ea36-164">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="4ea36-164">wwwroot folder</span></span>

<span data-ttu-id="4ea36-165">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4ea36-166">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4ea36-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4ea36-167">appSettings.json</span></span>

<span data-ttu-id="4ea36-168">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4ea36-169">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4ea36-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4ea36-170">Program.cs</span></span>

<span data-ttu-id="4ea36-171">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-171">Contains the entry point for the program.</span></span> <span data-ttu-id="4ea36-172">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4ea36-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4ea36-173">Startup.cs</span></span>

<span data-ttu-id="4ea36-174">앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="4ea36-175">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4ea36-176">다음 단계</span><span class="sxs-lookup"><span data-stu-id="4ea36-176">Next steps</span></span>

<span data-ttu-id="4ea36-177">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4ea36-178">모델 추가</span><span class="sxs-lookup"><span data-stu-id="4ea36-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ea36-179">이 시리즈의 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="4ea36-180">[시리즈](xref:tutorials/razor-pages/index)에서는 ASP.NET Core Razor Pages 웹앱을 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4ea36-181">시리즈가 끝나면 영화의 데이터베이스를 관리하는 앱이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4ea36-182">이 자습서에서는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ea36-183">Razor Pages 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4ea36-184">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-184">Run the app.</span></span>
> * <span data-ttu-id="4ea36-185">프로젝트 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-185">Examine the project files.</span></span>

<span data-ttu-id="4ea36-186">이 자습서의 내용을 마치면 이후의 자습서에서 빌드할 Razor Pages 웹앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4ea36-188">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4ea36-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ea36-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ea36-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ea36-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ea36-191">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4ea36-192">Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="4ea36-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ea36-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ea36-194">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="4ea36-195">새 ASP.NET Core 웹 애플리케이션을 만들고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4ea36-197">프로젝트 이름을 **RazorPagesMovie**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4ea36-198">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 프로젝트 이름을 *RazorPagesMovie*로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/config.png)

* <span data-ttu-id="4ea36-200">드롭다운에서 **ASP.NET Core 2.2**를 선택하고 **웹 애플리케이션**을 선택한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![새 ASP.NET Core 웹 응용 프로그램](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4ea36-202">다음 시작 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-202">The following starter project is created:</span></span>

  ![솔루션 탐색기](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ea36-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ea36-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ea36-205">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4ea36-206">프로젝트를 포함하는 디렉터리(`cd`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4ea36-207">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4ea36-208">`dotnet new` 명령은 *RazorPagesMovie* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4ea36-209">`code` 명령은 Visual Studio Code의 현재 인스턴스에서 *RazorPagesMovie* 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4ea36-210">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 다음 대화 상자가 나타납니다. **빌드 및 디버그에 필요한 자산이 ‘RazorPagesMovie’에서 누락되었습니다.  Add them?** 라는 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4ea36-211">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-211">Select **Yes**.</span></span>

  <span data-ttu-id="4ea36-212">*launch.json* 및 *tasks.json* 파일을 포함한 *.vscode* 디렉터리가 프로젝트의 루트 디렉터리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ea36-213">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4ea36-214">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-214">Select **File** > **New Solution**.</span></span>

![macOS 새 솔루션](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="4ea36-216">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="4ea36-217">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **웹 애플리케이션** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="4ea36-218">**새 ASP.NET Core 웹 API 구성** 대화 상자에서 **대상 프레임워크**를 **.NET Core 3.1**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 선택](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="4ea36-220">프로젝트 이름을 **RazorPagesMovie**로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4ea36-222">앱 실행</span><span class="sxs-lookup"><span data-stu-id="4ea36-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ea36-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ea36-224">Ctrl+F5를 눌러 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4ea36-225">Visual Studio가 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 시작하고 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4ea36-226">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4ea36-227">`localhost`가 로컬 컴퓨터의 표준 호스트 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4ea36-228">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4ea36-229">Visual Studio에서 웹 프로젝트를 만들 경우 웹 서버에 임의 포트가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="4ea36-230">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ea36-231">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4ea36-233">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-233">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="4ea36-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ea36-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4ea36-236">**Ctrl-F5** 키를 눌러서 디버거 없이 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4ea36-237">Visual Studio Code가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4ea36-238">주소 표시줄에는 `localhost:port#`이 표시되고 `example.com` 같은 주소는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4ea36-239">그 이유는 `localhost`가 로컬 컴퓨터의 표준 이름이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4ea36-240">Localhost는 로컬 컴퓨터의 웹 요청만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="4ea36-241">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ea36-242">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4ea36-244">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-244">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ea36-246">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ea36-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4ea36-247">디버거 없이 실행하려면 **Cmd-Opt-F5**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4ea36-248">Visual Studio가 [Kestrel](xref:fundamentals/servers/kestrel)을 시작하고, 브라우저를 시작하고, `http://localhost:5001`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="4ea36-249">앱의 홈페이지에서 **승인**을 선택하여 추적에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ea36-250">이 앱은 개인 정보를 추적하지 않지만 유럽 연합의 [GDPR(일반 데이터 보호 규정)](xref:security/gdpr)을 준수하기 위해 필요한 경우 프로젝트 템플릿에는 동의 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="4ea36-252">다음 이미지에서는 추적에 동의한 후에 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-252">The following image shows the app after you give consent to tracking:</span></span>

  ![홈 또는 인덱스 페이지](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4ea36-254">프로젝트 파일 검사</span><span class="sxs-lookup"><span data-stu-id="4ea36-254">Examine the project files</span></span>

<span data-ttu-id="4ea36-255">이후 자습서에서 작업할 주요 프로젝트 폴더 및 파일에 대한 개요는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4ea36-256">페이지 폴더</span><span class="sxs-lookup"><span data-stu-id="4ea36-256">Pages folder</span></span>

<span data-ttu-id="4ea36-257">Razor Pages 및 지원 파일이 들어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4ea36-258">각 Razor 페이지는 파일 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4ea36-259">Razor 구문을 사용하는 C# 코드로 HTML 태그를 포함하는 *.cshtml* 파일.</span><span class="sxs-lookup"><span data-stu-id="4ea36-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4ea36-260">페이지 이벤트를 처리하는 C# 코드가 포함된 *.cshtml.cs* 파일.</span><span class="sxs-lookup"><span data-stu-id="4ea36-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4ea36-261">지원 파일에는 밑줄로 시작하는 이름이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4ea36-262">예를 들어 *_Layout.cshtml* 파일은 모든 페이지에 공통되는 UI 요소를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4ea36-263">이 파일은 페이지 맨 위에 있는 탐색 메뉴를 설정하고 페이지 맨 아래에 저작권 표시를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4ea36-264">자세한 내용은 <xref:mvc/views/layout>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4ea36-265">wwwroot 폴더</span><span class="sxs-lookup"><span data-stu-id="4ea36-265">wwwroot folder</span></span>

<span data-ttu-id="4ea36-266">HTML 파일, JavaScript 파일 및 CSS 파일과 같은 정적 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4ea36-267">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4ea36-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4ea36-268">appSettings.json</span></span>

<span data-ttu-id="4ea36-269">연결 문자열과 같은 구성 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4ea36-270">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4ea36-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4ea36-271">Program.cs</span></span>

<span data-ttu-id="4ea36-272">프로그램의 진입점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-272">Contains the entry point for the program.</span></span> <span data-ttu-id="4ea36-273">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4ea36-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4ea36-274">Startup.cs</span></span>

<span data-ttu-id="4ea36-275">쿠키에 대한 동의 필요 여부 등 앱 동작을 구성하는 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="4ea36-276">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4ea36-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ea36-277">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4ea36-277">Additional resources</span></span>

* [<span data-ttu-id="4ea36-278">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4ea36-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="4ea36-279">다음 단계</span><span class="sxs-lookup"><span data-stu-id="4ea36-279">Next steps</span></span>

<span data-ttu-id="4ea36-280">시리즈의 다음 자습서로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4ea36-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4ea36-281">모델 추가</span><span class="sxs-lookup"><span data-stu-id="4ea36-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
