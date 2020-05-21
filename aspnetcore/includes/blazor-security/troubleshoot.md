## <a name="troubleshoot"></a><span data-ttu-id="5a86d-101">문제 해결</span><span class="sxs-lookup"><span data-stu-id="5a86d-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="5a86d-102">쿠키 및 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="5a86d-102">Cookies and site data</span></span>

<span data-ttu-id="5a86d-103">쿠키 및 사이트 데이터는 앱 업데이트에 걸쳐 유지 되 고 테스트 및 문제 해결에 방해가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="5a86d-104">앱 코드를 변경 하거나, 공급자를 사용 하 여 사용자 계정을 변경 하거나, 공급자 앱 구성 변경을 수행 하는 경우 다음을 선택 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="5a86d-105">사용자 로그인 쿠키</span><span class="sxs-lookup"><span data-stu-id="5a86d-105">User sign-in cookies</span></span>
* <span data-ttu-id="5a86d-106">앱 쿠키</span><span class="sxs-lookup"><span data-stu-id="5a86d-106">App cookies</span></span>
* <span data-ttu-id="5a86d-107">캐시 되 고 저장 된 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="5a86d-107">Cached and stored site data</span></span>

<span data-ttu-id="5a86d-108">느린 쿠키 및 사이트 데이터의 테스트 및 문제 해결을 방해 하는 한 가지 방법은 다음을 수행 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="5a86d-109">브라우저 구성</span><span class="sxs-lookup"><span data-stu-id="5a86d-109">Configure a browser</span></span>
  * <span data-ttu-id="5a86d-110">브라우저를 사용 하 여 브라우저를 닫을 때마다 모든 쿠키 및 사이트 데이터를 삭제 하도록 구성할 수 있는 테스트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-110">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="5a86d-111">응용 프로그램, 테스트 사용자 또는 공급자 구성에 대 한 변경 사항 사이에서 브라우저를 수동으로 또는 IDE에서 닫을지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-111">Make sure that the browser is closed manually or by the IDE between any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="5a86d-112">사용자 지정 명령을 사용 하 여 Visual Studio에서 incognito 또는 private 모드로 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-112">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="5a86d-113">Visual Studio의 **실행** 단추에서 **찾아보기** 대화 상자를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-113">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="5a86d-114">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-114">Select the **Add** button.</span></span>
  * <span data-ttu-id="5a86d-115">**프로그램** 필드에서 브라우저의 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-115">Provide the path to your browser in the **Program** field.</span></span>
  * <span data-ttu-id="5a86d-116">**인수** 필드에 브라우저에서 incognito 또는 private 모드와 앱의 URL을 여는 데 사용 하는 명령줄 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-116">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode and the URL of the app.</span></span> <span data-ttu-id="5a86d-117">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-117">For example:</span></span>
    * <span data-ttu-id="5a86d-118">Google Chrome &ndash;`--incognito --new-window https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5a86d-118">Google Chrome &ndash; `--incognito --new-window https://localhost:5001`</span></span>
    * <span data-ttu-id="5a86d-119">Mozilla Firefox &ndash;`-private -url https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="5a86d-119">Mozilla Firefox &ndash; `-private -url https://localhost:5001`</span></span>
  * <span data-ttu-id="5a86d-120">**이름 필드에** 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-120">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="5a86d-121">`Firefox Auth Testing`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-121">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="5a86d-122">**확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-122">Select the **OK** button.</span></span>
  * <span data-ttu-id="5a86d-123">앱을 사용 하 여 테스트를 반복할 때마다 브라우저 프로필을 선택 하지 않으려면 **기본 단추로 설정** 단추를 사용 하 여 프로필을 기본값으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-123">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="5a86d-124">응용 프로그램, 테스트 사용자 또는 공급자 구성에 대 한 변경 사항 사이에서 IDE가 브라우저를 닫았는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-124">Make sure that the browser is closed by the IDE between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="5a86d-125">서버 앱 실행</span><span class="sxs-lookup"><span data-stu-id="5a86d-125">Run the Server app</span></span>

<span data-ttu-id="5a86d-126">호스트 된 Blazor 앱을 테스트 하 고 문제를 해결할 때 **서버** 프로젝트에서 앱을 실행 하 고 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-126">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="5a86d-127">예를 들어 Visual Studio에서 다음 방법 중 하나를 사용 하 여 응용 프로그램을 시작 하기 전에 **솔루션 탐색기** 에서 서버 프로젝트가 강조 표시 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-127">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="5a86d-128">**실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-128">Select the **Run** button.</span></span>
* <span data-ttu-id="5a86d-129">메뉴에서 **디버그**  >  **디버깅 시작** 을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-129">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="5a86d-130"><kbd>F5</kbd>키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="5a86d-130">Press <kbd>F5</kbd>.</span></span>
