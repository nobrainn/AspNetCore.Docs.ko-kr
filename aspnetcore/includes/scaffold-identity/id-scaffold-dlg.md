<span data-ttu-id="7fece-101">Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7fece-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7fece-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7fece-103">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **Add**  >  **새 스 캐 폴드 항목**추가를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7fece-104">**새 스 캐 폴드 항목 추가** 대화 상자의 왼쪽 창에서 **id**  >  **추가**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="7fece-105">**Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="7fece-106">기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="7fece-107">`~/Pages/Shared/_Layout.cshtml`Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7fece-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="7fece-108">`~/Views/Shared/_Layout.cshtml`MVC 프로젝트의 경우</span><span class="sxs-lookup"><span data-stu-id="7fece-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="7fece-109">Blazor 서버 템플릿 ()에서 만든 Blazor 서버 앱은 `blazorserver` 기본적으로 Razor Pages 또는 MVC에 대해 구성 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="7fece-110">레이아웃 페이지 항목을 비워 둡니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="7fece-111">단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="7fece-112">기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="7fece-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="7fece-113">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7fece-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7fece-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7fece-115">이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="7fece-116">프로젝트 파일 (*.csproj*)에 필요한 NuGet 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="7fece-117">프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="7fece-118">다음 명령을 실행 하 여 Identity 스 캐 폴더 옵션을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="7fece-119">프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="7fece-120">예를 들어 기본 UI와 최소 파일 수를 사용 하 여 id를 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7fece-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
