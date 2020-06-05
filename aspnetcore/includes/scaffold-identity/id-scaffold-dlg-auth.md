::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8a99-101">Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8a99-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8a99-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8a99-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **Add** > **새 스 캐 폴드 항목**추가를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e8a99-104">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e8a99-105">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e8a99-106">레이아웃 파일을 잘못 된 태그로 덮어쓰지 않도록 기존 레이아웃 페이지를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="e8a99-107">기존 \* \_ 레이아웃\* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="e8a99-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="e8a99-108">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-108">For example:</span></span>
    * <span data-ttu-id="e8a99-109">`~/Pages/Shared/_Layout.cshtml`기존 Razor Pages 인프라를 사용 하는 Razor Pages 또는 Blazor 서버 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="e8a99-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="e8a99-110">`~/Views/Shared/_Layout.cshtml`기존 MVC 인프라를 사용 하는 MVC 프로젝트 또는 Blazor 서버 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="e8a99-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="e8a99-111">기존 데이터 컨텍스트를 사용 하려면 재정의할 파일을 하나 이상 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="e8a99-112">데이터 컨텍스트를 추가 하려면 파일을 하나 이상 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="e8a99-113">데이터 컨텍스트 클래스를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-113">Select your data context class.</span></span>
  * <span data-ttu-id="e8a99-114">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-114">Select **Add**.</span></span>
* <span data-ttu-id="e8a99-115">새 사용자 컨텍스트를 만들고 Id에 대 한 사용자 지정 사용자 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="e8a99-116">단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="e8a99-117">기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="e8a99-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="e8a99-118">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-118">Select **Add**.</span></span>

<span data-ttu-id="e8a99-119">참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8a99-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e8a99-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8a99-121">이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e8a99-122">프로젝트 파일 (*.csproj*)에 필요한 NuGet 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="e8a99-123">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="e8a99-124">다음 명령을 실행 하 여 Identity 스 캐 폴더 옵션을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="e8a99-125">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e8a99-126">예를 들어 기본 UI를 사용 하 여 id를 설정 하 고 최소 파일 수를 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="e8a99-127">DB 컨텍스트에 대 한 올바른 정규화 된 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="e8a99-128">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e8a99-129">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 파일 목록을 큰따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="e8a99-130">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e8a99-131">플래그 또는 플래그를 지정 하지 않고 Id 스 캐 폴더를 실행 하면 `--files` `--useDefaultUI` 사용 가능한 모든 id UI 페이지가 프로젝트에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8a99-132">Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8a99-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8a99-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8a99-134">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **Add** > **새 스 캐 폴드 항목**추가를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e8a99-135">**스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e8a99-136">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e8a99-137">기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="e8a99-138">기존 \* \_ 레이아웃\* 을 선택한 경우에는이 파일을 덮어쓰지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="e8a99-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="e8a99-139">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-139">For example:</span></span>
    * <span data-ttu-id="e8a99-140">`~/Pages/Shared/_Layout.cshtml`Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e8a99-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="e8a99-141">`~/Views/Shared/_Layout.cshtml`MVC 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="e8a99-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="e8a99-142">기존 데이터 컨텍스트를 사용 하려면 재정의할 파일을 하나 이상 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="e8a99-143">데이터 컨텍스트를 추가 하려면 파일을 하나 이상 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="e8a99-144">데이터 컨텍스트 클래스를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-144">Select your data context class.</span></span>
  * <span data-ttu-id="e8a99-145">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-145">Select **Add**.</span></span>
* <span data-ttu-id="e8a99-146">새 사용자 컨텍스트를 만들고 Id에 대 한 사용자 지정 사용자 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="e8a99-147">단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="e8a99-148">기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="e8a99-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="e8a99-149">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-149">Select **Add**.</span></span>

<span data-ttu-id="e8a99-150">참고: 새 사용자 컨텍스트를 만드는 경우 재정의할 파일을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8a99-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e8a99-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8a99-152">이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e8a99-153">프로젝트 파일 (*.csproj*)에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="e8a99-154">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e8a99-155">다음 명령을 실행 하 여 Identity 스 캐 폴더 옵션을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e8a99-156">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e8a99-157">예를 들어 기본 UI를 사용 하 여 id를 설정 하 고 최소 파일 수를 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="e8a99-158">DB 컨텍스트에 대 한 올바른 정규화 된 이름을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="e8a99-159">PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e8a99-160">PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 파일 목록을 큰따옴표로 묶습니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="e8a99-161">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e8a99-162">플래그 또는 플래그를 지정 하지 않고 Id 스 캐 폴더를 실행 하면 `--files` `--useDefaultUI` 사용 가능한 모든 id UI 페이지가 프로젝트에 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8a99-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
