---
<span data-ttu-id="c0eb6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c0eb6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-102">'Blazor'</span></span>
- <span data-ttu-id="c0eb6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-103">'Identity'</span></span>
- <span data-ttu-id="c0eb6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c0eb6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-105">'Razor'</span></span>
- <span data-ttu-id="c0eb6-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-106">'SignalR' uid:</span></span> 

---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="c0eb6-107">ASP.NET Core를 사용하는 Azure App Service 및 IIS에 대한 일반적인 오류 참조</span><span class="sxs-lookup"><span data-stu-id="c0eb6-107">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c0eb6-108">이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-108">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="c0eb6-109">일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-109">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="c0eb6-110">다음과 같은 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-110">Collect the following information:</span></span>

* <span data-ttu-id="c0eb6-111">브라우저 동작(상태 코드 및 오류 메시지)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-111">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="c0eb6-112">애플리케이션 이벤트 로그 항목</span><span class="sxs-lookup"><span data-stu-id="c0eb6-112">Application Event Log entries</span></span>
  * <span data-ttu-id="c0eb6-113">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-113">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="c0eb6-114">IIS</span><span class="sxs-lookup"><span data-stu-id="c0eb6-114">IIS</span></span>
    1. <span data-ttu-id="c0eb6-115">**Windows** 메뉴에서 **시작**을 선택하고, *이벤트 뷰어*를 입력하고, **Enter**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-115">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="c0eb6-116">**이벤트 뷰어**가 열리면 사이드바에서 **Windows 로그** > **애플리케이션**을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-116">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="c0eb6-117">ASP.NET Core 모듈 stdout 및 디버그 로그 항목</span><span class="sxs-lookup"><span data-stu-id="c0eb6-117">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="c0eb6-118">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-118">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="c0eb6-119">IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-119">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="c0eb6-120">오류 정보를 다음 일반 오류와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-120">Compare error information to the following common errors.</span></span> <span data-ttu-id="c0eb6-121">일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-121">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="c0eb6-122">이 항목의 오류 목록은 완전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-122">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="c0eb6-123">여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-123">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="c0eb6-124">OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-124">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="c0eb6-125">**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-125">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="c0eb6-126">데이터 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-126">The data is the error.</span></span>

<span data-ttu-id="c0eb6-127">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-127">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-128">OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-128">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="c0eb6-129">OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-129">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="c0eb6-130">OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-130">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="c0eb6-131">[.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-131">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="c0eb6-132">설치 관리자가 실행될 때 **복구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-132">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="c0eb6-133">사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-133">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="c0eb6-134">Azure App Services에서 호스트하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-134">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="c0eb6-135">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-135">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="c0eb6-136">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-136">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-137">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-137">Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-138">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-138">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-139">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-139">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="c0eb6-140">‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-140">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="c0eb6-141">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-141">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-142">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-142">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

* <span data-ttu-id="c0eb6-143">**ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-143">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-144">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-144">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="c0eb6-145">실패한 HRESULT가 반환되었습니다. 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-145">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="c0eb6-146">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-146">Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-147">호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-147">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-148">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-148">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="c0eb6-149">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-149">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-150">미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-150">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="c0eb6-151">**두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="c0eb6-151">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="c0eb6-152">ASP.NET Core {RUNTIME VERSION}(x86) 런타임</span><span class="sxs-lookup"><span data-stu-id="c0eb6-152">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="c0eb6-153">ASP.NET Core {RUNTIME VERSION}(x64) 런타임</span><span class="sxs-lookup"><span data-stu-id="c0eb6-153">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="c0eb6-154">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-154">Restart the app.</span></span> <span data-ttu-id="c0eb6-155">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-155">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="c0eb6-156">미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-156">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="c0eb6-157">사이트 확장을 제거한 후 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-157">After removing the site extension, restart the app.</span></span> <span data-ttu-id="c0eb6-158">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-158">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="c0eb6-159">미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-159">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="c0eb6-160">**애플리케이션 설정**에 있는 앱의 **플랫폼**이 앱의 비트 수와 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-160">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="c0eb6-161">자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-161">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="c0eb6-162">x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-162">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="c0eb6-163">**브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-163">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="c0eb6-164">**애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-164">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="c0eb6-165">자세한 내용은 stderr 로그를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-165">Please check the stderr logs for more information.</span></span> <span data-ttu-id="c0eb6-166">실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-166">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="c0eb6-167">CLR 작업자 스레드가 조기에 종료됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-167">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="c0eb6-168">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-168">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

* <span data-ttu-id="c0eb6-169">**ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="c0eb6-169">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="c0eb6-170">이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-170">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="c0eb6-171">RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).</span><span class="sxs-lookup"><span data-stu-id="c0eb6-171">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="c0eb6-172">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-172">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-173">x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-173">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="c0eb6-174">IIS 관리자에서 앱 풀의 **고급 설정**을 열고 **32비트 앱 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-174">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="c0eb6-175">플랫폼이 RID와 충돌함</span><span class="sxs-lookup"><span data-stu-id="c0eb6-175">Platform conflicts with RID</span></span>

* <span data-ttu-id="c0eb6-176">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-176">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="c0eb6-177">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-177">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="c0eb6-178">**ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-178">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="c0eb6-179">프로그램을 잘못된 형식으로 로드하려고 했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-179">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="c0eb6-180">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-180">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-181">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-181">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-182">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-182">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-183">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-183">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-184">Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-184">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="c0eb6-185">호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-185">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="c0eb6-186">URI 엔드포인트가 잘못되었거나 중지된 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="c0eb6-186">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="c0eb6-187">**브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-187">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="c0eb6-188">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-188">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-189">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-189">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-190">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-190">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-191">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-191">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-192">사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-192">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="c0eb6-193">바인딩을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-193">Check the bindings.</span></span>

* <span data-ttu-id="c0eb6-194">IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-194">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="c0eb6-195">CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-195">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="c0eb6-196">**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-196">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="c0eb6-197">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-197">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-198">적절한 역할 및 기능이 사용 가능한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-198">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="c0eb6-199">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-199">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="c0eb6-200">잘못된 웹 사이트 실제 경로 또는 누락된 앱</span><span class="sxs-lookup"><span data-stu-id="c0eb6-200">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="c0eb6-201">**브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-201">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="c0eb6-202">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-202">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-203">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-203">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-204">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-204">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-205">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-205">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-206">IIS 웹 사이트 **기본 설정**과 실제 앱 폴더를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-206">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="c0eb6-207">앱이 IIS 웹 사이트 **실제 경로**의 폴더에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-207">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="c0eb6-208">잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한</span><span class="sxs-lookup"><span data-stu-id="c0eb6-208">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="c0eb6-209">**브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-209">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="c0eb6-210">**--또는--** 이 페이지를 표시할 수 없습니다</span><span class="sxs-lookup"><span data-stu-id="c0eb6-210">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="c0eb6-211">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-211">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-212">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-212">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-213">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-213">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-214">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-214">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-215">적절한 역할을 사용할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-215">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="c0eb6-216">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-216">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="c0eb6-217">**프로그램 및 기능** 또는 **앱 및 기능**을 열고 **Windows Server 호스팅**이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-217">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="c0eb6-218">**Windows Server 호스팅**이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-218">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="c0eb6-219">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-219">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="c0eb6-220">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-220">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="c0eb6-221">**애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity**로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-221">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="c0eb6-222">ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-222">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="c0eb6-223">*%windir%/System32/inetsrv/config*에서 *applicationHost.config*를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-223">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="c0eb6-224">ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-224">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="c0eb6-225">또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-225">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="c0eb6-226">최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-226">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="c0eb6-227">잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반</span><span class="sxs-lookup"><span data-stu-id="c0eb6-227">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="c0eb6-228">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-228">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="c0eb6-229">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-229">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="c0eb6-230">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-230">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="c0eb6-231">애플리케이션 '{PATH}'를 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-231">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="c0eb6-232">'{PATH}'에서 실행 파일을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-232">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="c0eb6-233">애플리케이션 '/LM/W3SVC/2/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8007023e'.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-233">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="c0eb6-234">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-234">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-235">**ASP.NET Core 모듈 디버그 로그:** 이벤트 로그: '애플리케이션 '{PATH}'를 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-235">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="c0eb6-236">'{PATH}'에서 실행 파일을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-236">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="c0eb6-237">실패한 HRESULT가 반환되었습니다. 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="c0eb6-237">Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="c0eb6-238">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-238">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-239">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-239">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-240">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-240">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-241">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-241">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-242">*web.config*의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-242">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="c0eb6-243">FDD의 경우 *dotnet.exe*에서 PATH 설정을 통해 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-243">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="c0eb6-244">시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-244">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="c0eb6-245">FDD의 경우 *dotnet.exe*에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-245">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="c0eb6-246">앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-246">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="c0eb6-247">*C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-247">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="c0eb6-248">IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-248">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="c0eb6-249">서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-249">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="c0eb6-250">호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-250">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="c0eb6-251">.NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자**를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-251">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="c0eb6-252">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-252">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="c0eb6-253">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-253">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="c0eb6-254">특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-254">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="c0eb6-255">설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-255">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="c0eb6-256">\<aspNetCore> 요소의 인수가 잘못됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-256">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="c0eb6-257">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-257">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="c0eb6-258">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-258">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-259">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-259">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="c0eb6-260">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-260">Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-261">hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="c0eb6-261">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="c0eb6-262">다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 애플리케이션 '/LM/W3SVC/3/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-262">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="c0eb6-263">**ASP.NET Core 모듈 stdout 로그:** dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="c0eb6-263">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="c0eb6-264">[https://go.microsoft.com/fwlink/?LinkID=798306&amp;clcid=0x409](https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 ) 에서 dotnet SDK를 설치하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-264">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="c0eb6-265">**ASP.NET Core 모듈 디버그 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-265">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-266">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-266">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="c0eb6-267">실패한 HRESULT가 반환되었습니다. 0x8000ffff inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-267">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-268">hostfxr 호출에서 캡처된 출력: dotnet SDK 명령을 실행하시겠습니까?</span><span class="sxs-lookup"><span data-stu-id="c0eb6-268">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="c0eb6-269">다음 위치에서 dotnet SDK를 설치하세요. https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 실패한 HRESULT가 반환되었습니다. 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="c0eb6-269">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="c0eb6-270">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-270">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-271">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-271">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-272">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-272">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-273">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-273">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-274">*web.config*의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-274">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="c0eb6-275">.NET Core 공유 프레임워크 누락</span><span class="sxs-lookup"><span data-stu-id="c0eb6-275">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="c0eb6-276">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-276">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="c0eb6-277">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-277">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-278">이는 앱이 잘못 구성되었음을 의미할 가능성이 높으며, 앱이 대상으로 하고 머신에 설치되어 있는 Microsoft.NetCore.App 및 Microsoft.AspNetCore.App 버전을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-278">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="c0eb6-279">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-279">Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-280">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-280">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-281">지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-281">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="c0eb6-282">애플리케이션 '/LM/W3SVC/5/ROOT'를 시작하지 못했습니다. 오류 코드 '0x8000ffff'.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-282">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="c0eb6-283">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-283">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-284">지정된 프레임워크 'Microsoft.AspNetCore.App', 버전 '{VERSION}'을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-284">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="c0eb6-285">**ASP.NET Core 모듈 디버그 로그:** 실패한 HRESULT가 반환되었습니다. 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="c0eb6-285">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="c0eb6-286">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-286">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-287">FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-287">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="c0eb6-288">중지된 애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="c0eb6-288">Stopped Application Pool</span></span>

* <span data-ttu-id="c0eb6-289">**브라우저:** 503 서비스를 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-289">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="c0eb6-290">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-290">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-291">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-291">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-292">**ASP.NET Core 모듈 디버그 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-292">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-293">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-293">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-294">애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-294">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="c0eb6-295">하위 애플리케이션에 \<handlers> 섹션이 포함됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-295">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="c0eb6-296">**브라우저:** HTTP 오류 500.19 - 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-296">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="c0eb6-297">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-297">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-298">**ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-298">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="c0eb6-299">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-299">The sub-app's log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-300">**ASP.NET Core 모듈 디버그 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-300">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="c0eb6-301">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-301">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="c0eb6-302">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-302">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-303">하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않거나 하위 앱이 부모 앱의 처리기를 상속하지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-303">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="c0eb6-304">*web.config*의 부모 앱 `<system.webServer>` 섹션은 `<location>` 요소 내부에 배치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-304">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="c0eb6-305"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> 속성이 `false`로 설정되어 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 요소 내에서 지정된 설정이 부모 앱의 하위 디렉터리에 있는 앱에 상속되지 않음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-305">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="c0eb6-306">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-306">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="c0eb6-307">stdout 로그 경로가 올바르지 않음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-307">stdout log path incorrect</span></span>

* <span data-ttu-id="c0eb6-308">**브라우저:** 앱이 정상적으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-308">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="c0eb6-309">**애플리케이션 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-309">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="c0eb6-310">예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-310">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="c0eb6-311">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-311">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="c0eb6-312">예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-312">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="c0eb6-313">{PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-313">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="c0eb6-314">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-314">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="c0eb6-315">**ASP.NET Core 모듈 디버그 로그:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-315">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="c0eb6-316">예외 메시지: HRESULT 0x80070005가 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-316">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="c0eb6-317">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll에서 stdout 리디렉션을 중지하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-317">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="c0eb6-318">예외 메시지: HRESULT 0x80070002가 {PATH}에서 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-318">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="c0eb6-319">{PATH}\aspnetcorev2_inprocess.dll에서 stdout 리디렉션을 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-319">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

<span data-ttu-id="c0eb6-320">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-320">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-321">*web.config*의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-321">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="c0eb6-322">자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-322">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="c0eb6-323">앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-323">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="c0eb6-324">일반적인 애플리케이션 구성 문제</span><span class="sxs-lookup"><span data-stu-id="c0eb6-324">Application configuration general issue</span></span>

* <span data-ttu-id="c0eb6-325">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패 **--또는--** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-325">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="c0eb6-326">**애플리케이션 로그:** 변수</span><span class="sxs-lookup"><span data-stu-id="c0eb6-326">**Application Log:** Variable</span></span>

* <span data-ttu-id="c0eb6-327">**ASP.NET Core 모듈 stdout 로그:** 로그 파일은 생성되지만 비어 있거나 앱이 실패할 때까지 일반 항목으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-327">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="c0eb6-328">**ASP.NET Core 모듈 디버그 로그:** 변수</span><span class="sxs-lookup"><span data-stu-id="c0eb6-328">**ASP.NET Core Module Debug Log:** Variable</span></span>

<span data-ttu-id="c0eb6-329">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-329">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-330">앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-330">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="c0eb6-331">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-331">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c0eb6-332">이 토픽에서는 일반적인 오류에 대해 설명하고 Azure App Service 및 IIS에서 ASP.NET Core 앱을 호스트할 때 발생되는 특정 오류에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-332">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="c0eb6-333">일반적인 문제 해결 지침은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-333">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="c0eb6-334">다음과 같은 정보를 수집합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-334">Collect the following information:</span></span>

* <span data-ttu-id="c0eb6-335">브라우저 동작(상태 코드 및 오류 메시지)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-335">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="c0eb6-336">애플리케이션 이벤트 로그 항목</span><span class="sxs-lookup"><span data-stu-id="c0eb6-336">Application Event Log entries</span></span>
  * <span data-ttu-id="c0eb6-337">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-337">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="c0eb6-338">IIS</span><span class="sxs-lookup"><span data-stu-id="c0eb6-338">IIS</span></span>
    1. <span data-ttu-id="c0eb6-339">**Windows** 메뉴에서 **시작**을 선택하고, *이벤트 뷰어*를 입력하고, **Enter**를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-339">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="c0eb6-340">**이벤트 뷰어**가 열리면 사이드바에서 **Windows 로그** > **애플리케이션**을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-340">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="c0eb6-341">ASP.NET Core 모듈 stdout 및 디버그 로그 항목</span><span class="sxs-lookup"><span data-stu-id="c0eb6-341">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="c0eb6-342">Azure App Service: <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-342">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="c0eb6-343">IIS: ASP.NET Core 모듈 항목의 [로그 생성 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) 및 [향상된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-343">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="c0eb6-344">오류 정보를 다음 일반 오류와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-344">Compare error information to the following common errors.</span></span> <span data-ttu-id="c0eb6-345">일치하는 항목이 발견되면 문제 해결 권장 사항을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-345">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="c0eb6-346">이 항목의 오류 목록은 완전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-346">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="c0eb6-347">여기에 나열되지 않은 오류가 발생하는 경우 오류를 재현하는 방법에 대한 자세한 지침과 함께 이 항목 하단에 있는 **콘텐츠 피드백** 단추를 사용하여 새 문제를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-347">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="c0eb6-348">OS 업그레이드에서 32비트 ASP.NET Core 모듈이 제거됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-348">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="c0eb6-349">**애플리케이션 로그:** 모듈 DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-349">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="c0eb6-350">데이터 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-350">The data is the error.</span></span>

<span data-ttu-id="c0eb6-351">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-351">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-352">OS를 업그레이드하는 동안 **C:\Windows\SysWOW64\inetsrv** 디렉터리에 있는 비OS 파일은 보존되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-352">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="c0eb6-353">OS를 업그레이드하기 전에 ASP.NET Core 모듈을 설치한 다음, OS를 업그레이드한 후에 32비트 모드에서 앱 풀을 실행하면 이 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-353">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="c0eb6-354">OS를 업그레이드한 후에 ASP.NET Core 모듈을 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-354">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="c0eb6-355">[.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-355">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="c0eb6-356">설치 관리자가 실행될 때 **복구**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-356">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="c0eb6-357">사이트 확장 누락, 32비트(x86) 및 64비트(x64) 사이트 확장이 설치됨 또는 잘못된 프로세스 비트 수가 설정됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-357">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="c0eb6-358">Azure App Services에서 호스트하는 앱에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-358">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="c0eb6-359">**브라우저:** HTTP 오류 500.0 - ANCM In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-359">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="c0eb6-360">**애플리케이션 로그:** hostfxr를 호출하여 inprocess 요청 처리기를 찾는 데 실패했으며 네이티브 종속성을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-360">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="c0eb6-361">inprocess 요청 처리기를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-361">Could not find inprocess request handler.</span></span> <span data-ttu-id="c0eb6-362">hostfxr 호출에서 캡처된 출력: 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-362">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-363">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-363">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="c0eb6-364">‘/LM/W3SVC/1416782824/ROOT’ 애플리케이션을 시작하지 못했습니다. 오류 코드 ‘0x8000ffff’.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-364">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="c0eb6-365">**ASP.NET Core 모듈 stdout 로그:** 호환 가능한 프레임워크 버전을 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-365">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="c0eb6-366">지정된 프레임워크 ‘Microsoft.AspNetCore.App’, 버전 ‘{VERSION}-preview-\*’를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-366">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="c0eb6-367">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-367">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-368">미리 보기 런타임에서 앱을 실행하는 경우 앱의 비트 수 및 앱의 런타임 버전과 일치하는 32비트(x86) **또는** 64비트(x64) 사이트 확장을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-368">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="c0eb6-369">**두 확장을 모두 설치하거나 확장의 여러 런타임 버전을 설치하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="c0eb6-369">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="c0eb6-370">ASP.NET Core {RUNTIME VERSION}(x86) 런타임</span><span class="sxs-lookup"><span data-stu-id="c0eb6-370">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="c0eb6-371">ASP.NET Core {RUNTIME VERSION}(x64) 런타임</span><span class="sxs-lookup"><span data-stu-id="c0eb6-371">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="c0eb6-372">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-372">Restart the app.</span></span> <span data-ttu-id="c0eb6-373">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-373">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="c0eb6-374">미리 보기 런타임에서 앱을 실행 중이며 32비트(x86) 및 64비트(x64) [사이트 확장](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)이 둘 다 설치된 경우 앱의 비트 수와 일치하지 않는 사이트 확장을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-374">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="c0eb6-375">사이트 확장을 제거한 후 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-375">After removing the site extension, restart the app.</span></span> <span data-ttu-id="c0eb6-376">앱이 다시 시작될 때까지 몇 초간 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-376">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="c0eb6-377">미리 보기 런타임에서 앱을 실행 중이며 사이트 확장의 비트 수가 앱의 비트 수와 일치하는 경우 미리 보기 사이트 확장의 ‘런타임 버전’이 앱의 런타임 버전과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-377">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="c0eb6-378">**애플리케이션 설정**에 있는 앱의 **플랫폼**이 앱의 비트 수와 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-378">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="c0eb6-379">자세한 내용은 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-379">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="c0eb6-380">x86 앱이 배포되었지만 32비트 앱에 대해 앱 풀이 활성화되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-380">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="c0eb6-381">**브라우저:** HTTP 오류 500.30 - ANCM In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="c0eb6-381">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="c0eb6-382">**애플리케이션 로그:** 실제 루트 '{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 예기치 않은 관리 예외, 예외 코드 = '0xe0434352'가 적중했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-382">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="c0eb6-383">자세한 내용은 stderr 로그를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-383">Please check the stderr logs for more information.</span></span> <span data-ttu-id="c0eb6-384">실제 루트'{PATH}'가 있는 애플리케이션 '/LM/W3SVC/5/ROOT'에서 clr 및 관리되는 애플리케이션을 로드하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-384">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="c0eb6-385">CLR 작업자 스레드가 조기에 종료됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-385">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="c0eb6-386">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-386">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="c0eb6-387">이 시나리오는 자체 포함된 앱을 게시할 때 SDK에 의해 트래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-387">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="c0eb6-388">RID가 플랫폼 대상과 일치하지 않으면 SDK에서 오류가 발생합니다(예: 프로젝트 파일에 `<PlatformTarget>x86</PlatformTarget>`이 있는 `win10-x64` RID).</span><span class="sxs-lookup"><span data-stu-id="c0eb6-388">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="c0eb6-389">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-389">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-390">x86 프레임워크 종속 배포(`<PlatformTarget>x86</PlatformTarget>`)의 경우 32비트 앱용 IIS 앱 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-390">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="c0eb6-391">IIS 관리자에서 앱 풀의 **고급 설정**을 열고 **32비트 앱 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-391">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="c0eb6-392">플랫폼이 RID와 충돌함</span><span class="sxs-lookup"><span data-stu-id="c0eb6-392">Platform conflicts with RID</span></span>

* <span data-ttu-id="c0eb6-393">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-393">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="c0eb6-394">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-394">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="c0eb6-395">**ASP.NET Core 모듈 stdout 로그:** 처리되지 않은 예외: System.BadImageFormatException: 파일 또는 어셈블리 '{ASSEMBLY}.dll'을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-395">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="c0eb6-396">프로그램을 잘못된 형식으로 로드하려고 했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-396">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="c0eb6-397">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-397">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-398">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-398">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-399">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-399">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-400">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-400">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-401">Azure 앱 배포에서 앱을 업그레이드하고 새 어셈블리를 배포할 때 이 예외가 발생하면 이전 배포에서 모든 파일을 수동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-401">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="c0eb6-402">호환되지 않는 어셈블리가 오랫동안 남아 있으면 업그레이드된 응용 프로그램을 배포할 때 `System.BadImageFormatException` 예외가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-402">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="c0eb6-403">URI 엔드포인트가 잘못되었거나 중지된 웹 사이트</span><span class="sxs-lookup"><span data-stu-id="c0eb6-403">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="c0eb6-404">**브라우저:** ERR_CONNECTION_REFUSED **--또는--** 연결할 수 없음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-404">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="c0eb6-405">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-405">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-406">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-406">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-407">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-407">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-408">사용 중인 앱에 대해 올바른 URI 엔드포인트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-408">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="c0eb6-409">바인딩을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-409">Check the bindings.</span></span>

* <span data-ttu-id="c0eb6-410">IIS 웹 사이트가 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-410">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="c0eb6-411">CoreWebEngine 또는 W3SVC 서버 기능이 사용되지 않음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-411">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="c0eb6-412">**OS 예외:** ASP.NET Core 모듈을 사용하려면 IIS 7.0 CoreWebEngine 및 W3SVC 기능이 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-412">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="c0eb6-413">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-413">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-414">적절한 역할 및 기능이 사용 가능한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-414">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="c0eb6-415">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-415">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="c0eb6-416">잘못된 웹 사이트 실제 경로 또는 누락된 앱</span><span class="sxs-lookup"><span data-stu-id="c0eb6-416">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="c0eb6-417">**브라우저:** 403 사용할 수 없음 - 액세스가 거부되었습니다. **--또는--** 403.14 사용할 수 없음 - 웹 서버가 이 디렉터리의 내용을 표시하지 못하도록 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-417">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="c0eb6-418">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-418">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-419">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-419">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-420">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-420">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-421">IIS 웹 사이트 **기본 설정**과 실제 앱 폴더를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-421">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="c0eb6-422">앱이 IIS 웹 사이트 **실제 경로**의 폴더에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-422">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="c0eb6-423">잘못된 역할, 설치되지 않은 ASP.NET Core 모듈 또는 잘못된 권한</span><span class="sxs-lookup"><span data-stu-id="c0eb6-423">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="c0eb6-424">**브라우저:** 500.19 내부 서버 오류 - 요청된 페이지와 관련된 구성 데이터가 잘못되어 해당 페이지에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-424">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="c0eb6-425">**--또는--** 이 페이지를 표시할 수 없습니다</span><span class="sxs-lookup"><span data-stu-id="c0eb6-425">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="c0eb6-426">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-426">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-427">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-427">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-428">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-428">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-429">적절한 역할을 사용할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-429">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="c0eb6-430">[IIS 구성](xref:host-and-deploy/iis/index#iis-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-430">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="c0eb6-431">**프로그램 및 기능** 또는 **앱 및 기능**을 열고 **Windows Server 호스팅**이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-431">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="c0eb6-432">**Windows Server 호스팅**이 설치된 프로그램 목록에 없는 경우 .NET Core 호스팅 번들을 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-432">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="c0eb6-433">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-433">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="c0eb6-434">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-434">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="c0eb6-435">**애플리케이션 풀** > **프로세스 모델** > **Identity** 가 **ApplicationPoolIdentity**로 설정되어 있는지 또는 사용자 지정 ID에 앱의 배포 폴더에 액세스할 수 있는 올바른 권한이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-435">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="c0eb6-436">ASP.NET Core 호스팅 번들을 제거하고 이전 버전의 호스팅 번들을 설치하는 경우 *applicationHost.config* 파일에는 ASP.NET Core 모듈에 대한 섹션이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-436">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="c0eb6-437">*%windir%/System32/inetsrv/config*에서 *applicationHost.config*를 열고 `<configuration><configSections><sectionGroup name="system.webServer">` 섹션 그룹을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-437">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="c0eb6-438">ASP.NET Core 모듈에 대한 섹션이 섹션 그룹에서 누락된 경우 섹션 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-438">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="c0eb6-439">또는 최신 버전의 ASP.NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-439">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="c0eb6-440">최신 버전은 지원되는 이전 버전의 ASP.NET Core 앱과 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-440">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="c0eb6-441">잘못된 processPath, 누락된 PATH 변수, 설치되지 않은 호스팅 번들, 다시 시작되지 않은 시스템/IIS, 설치되지 않은 VC++ 재배포 가능 패키지 또는 dotnet.exe 액세스 위반</span><span class="sxs-lookup"><span data-stu-id="c0eb6-441">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="c0eb6-442">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-442">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="c0eb6-443">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"{...}" 명령줄로 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-443">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="c0eb6-444">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-444">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="c0eb6-445">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-445">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="c0eb6-446">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-446">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-447">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-447">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-448">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-448">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-449">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-449">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-450">*web.config*의 `<aspNetCore>` 요소에서 *processPath* 특성을 확인하여 FDD(프레임워크 종속 배포)에 대한 `dotnet`인지 또는 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)에 대한 `.\{ASSEMBLY}.exe`인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-450">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="c0eb6-451">FDD의 경우 *dotnet.exe*에서 PATH 설정을 통해 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-451">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="c0eb6-452">시스템 PATH 설정에 *C:\Program Files\dotnet\\* 이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-452">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="c0eb6-453">FDD의 경우 *dotnet.exe*에서 앱 풀의 사용자 ID에 액세스하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-453">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="c0eb6-454">앱 풀 사용자 ID에 *C:\Program Files\dotnet* 디렉터리에 대한 액세스 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-454">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="c0eb6-455">*C:\Program Files\dotnet* 및 앱 디렉터리에 앱 풀 사용자 ID에 대해 구성된 거부 규칙이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-455">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="c0eb6-456">IIS를 다시 시작하지 않은 상태로 FDD를 배포하고 .NET Core를 설치했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-456">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="c0eb6-457">서버를 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-457">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="c0eb6-458">호스팅 시스템에 .NET Core 런타임을 설치하지 않고 FDD를 배포했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-458">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="c0eb6-459">.NET Core 런타임이 설치되어 있지 않으면 시스템에서 **.NET Core 호스팅 번들 설치 관리자**를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-459">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="c0eb6-460">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="c0eb6-460">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="c0eb6-461">자세한 내용은 [.NET Core 호스팅 번들 설치](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-461">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="c0eb6-462">특정 런타임이 필요한 경우 [.NET Download Archives](https://dotnet.microsoft.com/download/archives)에서 런타임을 다운로드하여 시스템에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-462">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="c0eb6-463">설치를 완료하려면 시스템을 다시 시작하거나 명령 프롬프트에서 **net stop was /y**에 이어 **net start w3svc**를 실행하여 IIS를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-463">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="c0eb6-464">\<aspNetCore> 요소의 인수가 잘못됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-464">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="c0eb6-465">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-465">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="c0eb6-466">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"dotnet" .\{ASSEMBLY}.dll' 명령줄로 프로세스를 시작하지 못했습니다. 오류 코드 = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-466">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="c0eb6-467">**ASP.NET Core 모듈 stdout 로그:** 실행할 애플리케이션이 없습니다. 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-467">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

<span data-ttu-id="c0eb6-468">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-468">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-469">앱이 Kestrel에서 로컬로 실행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-469">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="c0eb6-470">프로세스 오류는 앱 내의 문제 때문일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-470">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="c0eb6-471">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-471">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="c0eb6-472">*web.config*의 `<aspNetCore>` 요소에서 *인수* 특성을 검사하여 (a) FDD(프레임워크 종속 배포)에 대한 `.\{ASSEMBLY}.dll`인지, 또는 (b) 없는 경우 빈 문자열(`arguments=""`)이거나 SCD(자체 포함 배포)에 대한 앱의 인수(`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 목록인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-472">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

<span data-ttu-id="c0eb6-473">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-473">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-474">FDD(프레임워크 종속 배포)의 경우 시스템에 올바른 런타임이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-474">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="c0eb6-475">중지된 애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="c0eb6-475">Stopped Application Pool</span></span>

* <span data-ttu-id="c0eb6-476">**브라우저:** 503 서비스를 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-476">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="c0eb6-477">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-477">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-478">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-478">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-479">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-479">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-480">애플리케이션 풀이 ‘중지됨’ 상태가 아닌지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-480">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="c0eb6-481">하위 애플리케이션에 \<handlers> 섹션이 포함됨</span><span class="sxs-lookup"><span data-stu-id="c0eb6-481">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="c0eb6-482">**브라우저:** HTTP 오류 500.19 - 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-482">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="c0eb6-483">**애플리케이션 로그:** 진입 금지</span><span class="sxs-lookup"><span data-stu-id="c0eb6-483">**Application Log:** No entry</span></span>

* <span data-ttu-id="c0eb6-484">**ASP.NET Core 모듈 stdout 로그:** 루트 앱의 로그 파일이 생성되고 정상 작동을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-484">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="c0eb6-485">하위 앱의 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-485">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="c0eb6-486">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-486">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-487">하위 앱의 *web.config* 파일에 `<handlers>` 섹션이 포함되어 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-487">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="c0eb6-488">stdout 로그 경로가 올바르지 않음</span><span class="sxs-lookup"><span data-stu-id="c0eb6-488">stdout log path incorrect</span></span>

* <span data-ttu-id="c0eb6-489">**브라우저:** 앱이 정상적으로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-489">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="c0eb6-490">**애플리케이션 로그:** 경고: stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log를 만들지 못했습니다. 오류 코드 = -2147024893.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-490">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="c0eb6-491">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-491">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="c0eb6-492">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-492">Troubleshooting:</span></span>

* <span data-ttu-id="c0eb6-493">*web.config*의 `<aspNetCore>` 요소에 지정된 `stdoutLogFile` 경로가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-493">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="c0eb6-494">자세한 내용은 [ASP.NET Core 모듈: 로그 만들기 및 리디렉션](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-494">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="c0eb6-495">앱 풀 사용자는 stdout 로그 경로에 대한 쓰기 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-495">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="c0eb6-496">일반적인 애플리케이션 구성 문제</span><span class="sxs-lookup"><span data-stu-id="c0eb6-496">Application configuration general issue</span></span>

* <span data-ttu-id="c0eb6-497">**브라우저:** HTTP 오류 502.5 - 프로세스 오류</span><span class="sxs-lookup"><span data-stu-id="c0eb6-497">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="c0eb6-498">**애플리케이션 로그:** 실제 루트 'C:\{PATH}\'가 있는 애플리케이션 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}'에서 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 명령줄로 프로세스를 만들었지만 지정된 포트 '{PORT}'에서 크래시하거나 응답하지 않거나 수신 대기하지 않습니다. 오류 코드 = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="c0eb6-498">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="c0eb6-499">**ASP.NET Core 모듈 stdout 로그:** 로그 파일이 생성되었지만 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-499">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="c0eb6-500">문제 해결:</span><span class="sxs-lookup"><span data-stu-id="c0eb6-500">Troubleshooting:</span></span>

<span data-ttu-id="c0eb6-501">앱 구성 또는 프로그래밍 문제로 인해 프로세스를 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-501">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="c0eb6-502">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c0eb6-502">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
