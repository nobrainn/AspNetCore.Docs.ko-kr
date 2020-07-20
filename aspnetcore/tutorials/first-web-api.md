---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212624"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="b1622-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="b1622-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="b1622-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="b1622-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="b1622-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1622-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b1622-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-107">Create a web API project.</span></span>
> * <span data-ttu-id="b1622-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b1622-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="b1622-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="b1622-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-111">Call the web API with Postman.</span></span>

<span data-ttu-id="b1622-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="b1622-113">개요</span><span class="sxs-lookup"><span data-stu-id="b1622-113">Overview</span></span>

<span data-ttu-id="b1622-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b1622-115">API</span><span class="sxs-lookup"><span data-stu-id="b1622-115">API</span></span> | <span data-ttu-id="b1622-116">설명</span><span class="sxs-lookup"><span data-stu-id="b1622-116">Description</span></span> | <span data-ttu-id="b1622-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="b1622-117">Request body</span></span> | <span data-ttu-id="b1622-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="b1622-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="b1622-119">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="b1622-119">Get all to-do items</span></span> | <span data-ttu-id="b1622-120">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-120">None</span></span> | <span data-ttu-id="b1622-121">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="b1622-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="b1622-122">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="b1622-122">Get an item by ID</span></span> | <span data-ttu-id="b1622-123">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-123">None</span></span> | <span data-ttu-id="b1622-124">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="b1622-125">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-125">Add a new item</span></span> | <span data-ttu-id="b1622-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-126">To-do item</span></span> | <span data-ttu-id="b1622-127">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="b1622-128">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b1622-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-129">To-do item</span></span> | <span data-ttu-id="b1622-130">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-130">None</span></span> |
|<span data-ttu-id="b1622-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="b1622-132">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b1622-133">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-133">None</span></span> | <span data-ttu-id="b1622-134">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-134">None</span></span>|

<span data-ttu-id="b1622-135">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-135">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b1622-141">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b1622-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b1622-145">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="b1622-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-147">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b1622-148">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b1622-149">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b1622-150">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="b1622-151">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-151">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b1622-154">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b1622-155">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b1622-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="b1622-157">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="b1622-158">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="b1622-158">The preceding commands:</span></span>

  * <span data-ttu-id="b1622-159">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="b1622-160">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-161">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b1622-162">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-162">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b1622-164">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b1622-165">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="b1622-167">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="b1622-168">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-168">Select **Next**.</span></span>

* <span data-ttu-id="b1622-169">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="b1622-171">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="b1622-172">API 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-172">Test the API</span></span>

<span data-ttu-id="b1622-173">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="b1622-174">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b1622-176">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b1622-177">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b1622-178">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b1622-179">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b1622-181">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b1622-182">브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-183">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b1622-184">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b1622-185">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b1622-186">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b1622-187">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="b1622-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="b1622-188">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b1622-189">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-189">Add a model class</span></span>

<span data-ttu-id="b1622-190">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b1622-191">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-193">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b1622-194">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b1622-195">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="b1622-196">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b1622-197">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b1622-198">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b1622-200">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b1622-201">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-202">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b1622-203">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-203">Right-click the project.</span></span> <span data-ttu-id="b1622-204">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b1622-205">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-205">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b1622-207">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b1622-208">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b1622-209">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="b1622-210">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b1622-211">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b1622-212">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-212">Add a database context</span></span>

<span data-ttu-id="b1622-213">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b1622-214">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="b1622-216">Microsoft.EntityFrameworkCore.SqlServer 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="b1622-217">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="b1622-218">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="b1622-219">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="b1622-220">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="b1622-221">위 지침에 따라 `Microsoft.EntityFrameworkCore.InMemory` NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="b1622-223">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="b1622-224">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b1622-225">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b1622-226">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b1622-227">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b1622-228">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b1622-229">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="b1622-229">Register the database context</span></span>

<span data-ttu-id="b1622-230">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b1622-231">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="b1622-232">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="b1622-233">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="b1622-233">The preceding code:</span></span>

* <span data-ttu-id="b1622-234">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b1622-235">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b1622-236">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="b1622-237">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="b1622-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-239">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b1622-240">**추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b1622-241">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러**를 선택하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="b1622-242">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="b1622-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="b1622-243">**모델 클래스**에서 **TodoItem (TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="b1622-244">**데이터 컨텍스트 클래스**에서 **TodoContext (TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="b1622-245">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b1622-246">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b1622-247">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="b1622-248">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="b1622-248">The preceding commands:</span></span>

* <span data-ttu-id="b1622-249">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="b1622-250">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="b1622-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="b1622-251">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="b1622-252">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="b1622-252">The generated code:</span></span>

* <span data-ttu-id="b1622-253">[`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b1622-254">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b1622-255">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b1622-256">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b1622-257">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="b1622-258">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="b1622-259">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="b1622-260">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="b1622-261">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="b1622-262">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="b1622-263">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="b1622-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="b1622-264">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="b1622-265">위의 코드는 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b1622-266">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b1622-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="b1622-268">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="b1622-269">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b1622-270">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="b1622-271">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="b1622-272">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b1622-273">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b1622-274">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="b1622-275">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-275">Install Postman</span></span>

<span data-ttu-id="b1622-276">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b1622-277">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="b1622-278">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-278">Start the web app.</span></span>
* <span data-ttu-id="b1622-279">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-279">Start Postman.</span></span>
* <span data-ttu-id="b1622-280">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="b1622-281">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="b1622-282">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="b1622-283">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="b1622-284">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-284">Create a new request.</span></span>
* <span data-ttu-id="b1622-285">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b1622-286">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="b1622-287">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b1622-288">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b1622-289">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b1622-290">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-290">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b1622-292">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-292">Test the location header URI</span></span>

* <span data-ttu-id="b1622-293">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b1622-294">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-294">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="b1622-296">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-296">Set the method to GET.</span></span>
* <span data-ttu-id="b1622-297">URI(예: `https://localhost:5001/api/TodoItems/1`)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b1622-298">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="b1622-299">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="b1622-299">Examine the GET methods</span></span>

<span data-ttu-id="b1622-300">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="b1622-301">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="b1622-302">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b1622-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="b1622-303">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="b1622-304">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-304">Test Get with Postman</span></span>

* <span data-ttu-id="b1622-305">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-305">Create a new request.</span></span>
* <span data-ttu-id="b1622-306">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="b1622-307">요청 URL을 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="b1622-308">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="b1622-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="b1622-309">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b1622-310">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-310">Select **Send**.</span></span>

<span data-ttu-id="b1622-311">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-311">This app uses an in-memory database.</span></span> <span data-ttu-id="b1622-312">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="b1622-313">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="b1622-314">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="b1622-314">Routing and URL paths</span></span>

<span data-ttu-id="b1622-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b1622-316">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b1622-317">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="b1622-318">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b1622-319">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems**Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="b1622-320">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b1622-321">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b1622-322">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-322">This sample doesn't use a template.</span></span> <span data-ttu-id="b1622-323">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b1622-324">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b1622-325">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b1622-326">반환 값</span><span class="sxs-lookup"><span data-stu-id="b1622-326">Return values</span></span>

<span data-ttu-id="b1622-327">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b1622-328">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b1622-329">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b1622-330">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b1622-331">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b1622-332">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b1622-333">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b1622-334">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b1622-335">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="b1622-336">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="b1622-336">The PutTodoItem method</span></span>

<span data-ttu-id="b1622-337">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="b1622-338">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b1622-339">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b1622-340">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b1622-341">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b1622-342">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b1622-343">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="b1622-344">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b1622-345">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b1622-346">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b1622-347">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b1622-348">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-348">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="b1622-350">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="b1622-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="b1622-351">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b1622-352">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b1622-353">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b1622-354">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b1622-355">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="b1622-356">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="b1622-357">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="b1622-357">Prevent over-posting</span></span>

<span data-ttu-id="b1622-358">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="b1622-359">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="b1622-360">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="b1622-361">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="b1622-362">이 문서에서는 **DTO**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="b1622-363">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-363">A DTO may be used to:</span></span>

* <span data-ttu-id="b1622-364">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-364">Prevent over-posting.</span></span>
* <span data-ttu-id="b1622-365">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="b1622-366">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="b1622-367">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="b1622-368">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="b1622-369">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="b1622-370">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="b1622-371">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="b1622-372">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="b1622-373">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="b1622-374">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b1622-375">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="b1622-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="b1622-376">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1622-377">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b1622-378">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-378">Create a web API project.</span></span>
> * <span data-ttu-id="b1622-379">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="b1622-380">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-380">Add a controller.</span></span>
> * <span data-ttu-id="b1622-381">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="b1622-382">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="b1622-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="b1622-383">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="b1622-383">Specify return values.</span></span>
> * <span data-ttu-id="b1622-384">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="b1622-385">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="b1622-386">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="b1622-387">개요</span><span class="sxs-lookup"><span data-stu-id="b1622-387">Overview</span></span>

<span data-ttu-id="b1622-388">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="b1622-389">API</span><span class="sxs-lookup"><span data-stu-id="b1622-389">API</span></span> | <span data-ttu-id="b1622-390">설명</span><span class="sxs-lookup"><span data-stu-id="b1622-390">Description</span></span> | <span data-ttu-id="b1622-391">요청 본문</span><span class="sxs-lookup"><span data-stu-id="b1622-391">Request body</span></span> | <span data-ttu-id="b1622-392">응답 본문</span><span class="sxs-lookup"><span data-stu-id="b1622-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="b1622-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b1622-393">GET /api/TodoItems</span></span> | <span data-ttu-id="b1622-394">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="b1622-394">Get all to-do items</span></span> | <span data-ttu-id="b1622-395">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-395">None</span></span> | <span data-ttu-id="b1622-396">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="b1622-396">Array of to-do items</span></span>|
|<span data-ttu-id="b1622-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b1622-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="b1622-398">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="b1622-398">Get an item by ID</span></span> | <span data-ttu-id="b1622-399">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-399">None</span></span> | <span data-ttu-id="b1622-400">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-400">To-do item</span></span>|
|<span data-ttu-id="b1622-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="b1622-401">POST /api/TodoItems</span></span> | <span data-ttu-id="b1622-402">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-402">Add a new item</span></span> | <span data-ttu-id="b1622-403">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-403">To-do item</span></span> | <span data-ttu-id="b1622-404">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-404">To-do item</span></span> |
|<span data-ttu-id="b1622-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="b1622-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="b1622-406">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="b1622-407">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="b1622-407">To-do item</span></span> | <span data-ttu-id="b1622-408">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-408">None</span></span> |
|<span data-ttu-id="b1622-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="b1622-410">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="b1622-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="b1622-411">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-411">None</span></span> | <span data-ttu-id="b1622-412">없음</span><span class="sxs-lookup"><span data-stu-id="b1622-412">None</span></span>|

<span data-ttu-id="b1622-413">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-413">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="b1622-419">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="b1622-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-422">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="b1622-423">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="b1622-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-425">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b1622-426">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="b1622-427">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="b1622-428">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="b1622-429">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="b1622-430">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="b1622-430">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b1622-433">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b1622-434">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="b1622-435">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="b1622-436">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="b1622-437">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-438">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b1622-439">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-439">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="b1622-441">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="b1622-442">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="b1622-443">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="b1622-444">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-444">Select **Next**.</span></span>

* <span data-ttu-id="b1622-445">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="b1622-447">API 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-447">Test the API</span></span>

<span data-ttu-id="b1622-448">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-448">The project template creates a `values` API.</span></span> <span data-ttu-id="b1622-449">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b1622-451">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b1622-452">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="b1622-453">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="b1622-454">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b1622-456">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="b1622-457">브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-458">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b1622-459">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="b1622-460">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="b1622-461">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="b1622-462">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="b1622-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="b1622-463">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="b1622-464">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-464">Add a model class</span></span>

<span data-ttu-id="b1622-465">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="b1622-466">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-468">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="b1622-469">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b1622-470">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="b1622-471">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b1622-472">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="b1622-473">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b1622-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b1622-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b1622-475">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="b1622-476">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1622-477">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b1622-478">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-478">Right-click the project.</span></span> <span data-ttu-id="b1622-479">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="b1622-480">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-480">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="b1622-482">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="b1622-483">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="b1622-484">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="b1622-485">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="b1622-486">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="b1622-487">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-487">Add a database context</span></span>

<span data-ttu-id="b1622-488">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="b1622-489">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-491">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="b1622-492">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b1622-493">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b1622-494">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="b1622-495">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="b1622-496">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="b1622-496">Register the database context</span></span>

<span data-ttu-id="b1622-497">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b1622-498">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="b1622-499">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="b1622-500">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="b1622-500">The preceding code:</span></span>

* <span data-ttu-id="b1622-501">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="b1622-502">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="b1622-503">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b1622-504">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-506">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="b1622-507">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="b1622-508">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="b1622-509">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b1622-511">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b1622-512">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="b1622-513">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="b1622-514">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="b1622-514">The preceding code:</span></span>

* <span data-ttu-id="b1622-515">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="b1622-516">[`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="b1622-517">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="b1622-518">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="b1622-519">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="b1622-520">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="b1622-521">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="b1622-522">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="b1622-523">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="b1622-524">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="b1622-525">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-525">Add Get methods</span></span>

<span data-ttu-id="b1622-526">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="b1622-527">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="b1622-528">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-528">Stop the app if it's still running.</span></span> <span data-ttu-id="b1622-529">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="b1622-530">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="b1622-531">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b1622-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="b1622-532">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="b1622-533">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="b1622-533">Routing and URL paths</span></span>

<span data-ttu-id="b1622-534">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="b1622-535">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="b1622-536">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="b1622-537">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="b1622-538">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="b1622-539">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="b1622-540">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="b1622-541">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-541">This sample doesn't use a template.</span></span> <span data-ttu-id="b1622-542">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="b1622-543">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="b1622-544">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="b1622-545">반환 값</span><span class="sxs-lookup"><span data-stu-id="b1622-545">Return values</span></span>

<span data-ttu-id="b1622-546">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="b1622-547">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="b1622-548">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="b1622-549">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="b1622-550">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="b1622-551">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="b1622-552">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="b1622-553">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="b1622-554">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="b1622-555">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="b1622-556">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="b1622-557">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="b1622-558">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-558">Start the web app.</span></span>
* <span data-ttu-id="b1622-559">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-559">Start Postman.</span></span>
* <span data-ttu-id="b1622-560">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1622-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b1622-562">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b1622-563">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1622-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b1622-564">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="b1622-565">또는 렌치를 선택하고 **설정**을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="b1622-566">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="b1622-567">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-567">Create a new request.</span></span>
  * <span data-ttu-id="b1622-568">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="b1622-569">요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="b1622-570">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="b1622-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="b1622-571">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="b1622-572">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-572">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="b1622-574">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-574">Add a Create method</span></span>

<span data-ttu-id="b1622-575">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="b1622-576">위의 코드는 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="b1622-577">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="b1622-578">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="b1622-579">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="b1622-580">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="b1622-581">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="b1622-582">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="b1622-583">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="b1622-584">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="b1622-585">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="b1622-586">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="b1622-587">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-587">Build the project.</span></span>
* <span data-ttu-id="b1622-588">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="b1622-589">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="b1622-590">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="b1622-591">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="b1622-592">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="b1622-593">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-593">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="b1622-595">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="b1622-596">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-596">Test the location header URI</span></span>

* <span data-ttu-id="b1622-597">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="b1622-598">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-598">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="b1622-600">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-600">Set the method to GET.</span></span>
* <span data-ttu-id="b1622-601">URI(예: `https://localhost:5001/api/Todo/2`)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="b1622-602">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="b1622-603">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="b1622-604">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="b1622-605">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="b1622-606">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="b1622-607">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="b1622-608">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="b1622-609">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="b1622-610">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="b1622-611">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="b1622-612">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="b1622-613">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="b1622-614">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="b1622-615">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-615">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="b1622-617">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="b1622-618">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="b1622-619">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="b1622-620">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="b1622-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="b1622-621">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="b1622-622">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="b1622-623">삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="b1622-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="b1622-624">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-624">Select **Send**.</span></span>

<span data-ttu-id="b1622-625">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="b1622-626">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="b1622-627">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="b1622-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="b1622-628">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="b1622-629">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-629">jQuery initiates the request.</span></span> <span data-ttu-id="b1622-630">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="b1622-631">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="b1622-632">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="b1622-633">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="b1622-634">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="b1622-635">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="b1622-636">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="b1622-637">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="b1622-638">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="b1622-639">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="b1622-640">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="b1622-641">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="b1622-642">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="b1622-642">Get a list of to-do items</span></span>

<span data-ttu-id="b1622-643">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="b1622-644">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="b1622-645">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="b1622-646">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-646">Add a to-do item</span></span>

<span data-ttu-id="b1622-647">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="b1622-648">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="b1622-649">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="b1622-650">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="b1622-651">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="b1622-651">Update a to-do item</span></span>

<span data-ttu-id="b1622-652">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="b1622-653">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="b1622-654">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="b1622-654">Delete a to-do item</span></span>

<span data-ttu-id="b1622-655">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b1622-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="b1622-656">웹 API에 인증 지원 추가</span><span class="sxs-lookup"><span data-stu-id="b1622-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="b1622-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="b1622-657">Server4.md)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1622-658">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b1622-658">Additional resources</span></span>

<span data-ttu-id="b1622-659">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="b1622-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="b1622-660">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b1622-661">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b1622-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="b1622-662">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="b1622-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
