---
title: Azure App Service 및 IIS에서 ASP.NET Core 문제 해결
author: rick-anderson
description: ASP.NET Core 앱의 Azure App Service 및 IIS(인터넷 정보 서비스) 배포에 대한 문제 진단 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 65095f3990c72224d95f1f5fe46d320ab8f12040
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404836"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="87dbf-103">Azure App Service 및 IIS에서 ASP.NET Core 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="87dbf-104">작성자 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="87dbf-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87dbf-105">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="87dbf-106">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="87dbf-107">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="87dbf-108">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="87dbf-109">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="87dbf-110">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="87dbf-111">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="87dbf-112">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="87dbf-113">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="87dbf-114">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="87dbf-115">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="87dbf-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="87dbf-116">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="87dbf-117">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-117">App startup errors</span></span>

<span data-ttu-id="87dbf-118">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="87dbf-119">로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패*는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="87dbf-120">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="87dbf-120">403.14 Forbidden</span></span>

<span data-ttu-id="87dbf-121">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-121">The app fails to start.</span></span> <span data-ttu-id="87dbf-122">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="87dbf-123">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="87dbf-124">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-125">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-126">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="87dbf-127">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-127">Perform the following steps:</span></span>

1. <span data-ttu-id="87dbf-128">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-129">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="87dbf-130">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="87dbf-131">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="87dbf-132">IIS에서 앱을 호스트하는 경우 **IIS 관리자**의 **기본 설정**에 표시되는 IIS **실제 경로**에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="87dbf-133">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="87dbf-134">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="87dbf-135">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="87dbf-136">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-136">500 Internal Server Error</span></span>

<span data-ttu-id="87dbf-137">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="87dbf-138">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="87dbf-139">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="87dbf-140">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="87dbf-141">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="87dbf-142">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="87dbf-143">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="87dbf-144">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="87dbf-145">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-145">The worker process fails.</span></span> <span data-ttu-id="87dbf-146">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-146">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-147">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 구성 요소를 로드하는 중 알 수 없는 오류가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="87dbf-148">다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-148">Take one of the following actions:</span></span>

* <span data-ttu-id="87dbf-149">[Microsoft 지원](https://support.microsoft.com/oas/default.aspx?prid=15832)에 문의하세요(**개발자 도구**를 선택한 다음, **ASP.NET Core** 선택).</span><span class="sxs-lookup"><span data-stu-id="87dbf-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="87dbf-150">Stack Overflow에 대해 질문하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="87dbf-151">[GitHub 리포지토리](https://github.com/dotnet/AspNetCore)에 문제를 제기하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="87dbf-152">500.30 In-Process 시작 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="87dbf-153">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-153">The worker process fails.</span></span> <span data-ttu-id="87dbf-154">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-154">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-155">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR in-process를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="87dbf-156">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="87dbf-157">일반적인 오류 조건:</span><span class="sxs-lookup"><span data-stu-id="87dbf-157">Common failure conditions:</span></span>

* <span data-ttu-id="87dbf-158">존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="87dbf-159">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="87dbf-160">Azure Key Vault를 사용할 경우 Key Vault에 대한 사용 권한이 부족합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="87dbf-161">대상 Key Vault의 액세스 정책을 확인하여 올바른 사용 권한이 부여되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="87dbf-162">500.31 ANCM 네이티브 종속성을 찾지 못함</span><span class="sxs-lookup"><span data-stu-id="87dbf-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="87dbf-163">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-163">The worker process fails.</span></span> <span data-ttu-id="87dbf-164">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-164">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-165">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 진행 중인 .NET Core 런타임을 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="87dbf-166">이 시작 오류의 가장 일반적인 원인은 `Microsoft.NETCore.App` 또는 `Microsoft.AspNetCore.App` 런타임이 설치되어 있지 않은 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="87dbf-167">앱이 대상 ASP.NET Core 3.0에 배포되고 해당 버전이 머신에 없는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="87dbf-168">예제 오류 메시지는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="87dbf-169">오류 메시지는 설치된 모든 .NET Core 버전과 앱에서 요청한 버전을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="87dbf-170">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-170">To fix this error, either:</span></span>

* <span data-ttu-id="87dbf-171">머신에 적절한 버전의 .NET Core를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="87dbf-172">머신에 있는 .NET Core 버전을 대상으로 앱을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="87dbf-173">[자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="87dbf-174">개발 중에 실행될 때(`ASPNETCORE_ENVIRONMENT` 환경 변수가 `Development`로 설정됨) 특정 오류가 HTTP 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="87dbf-175">프로세스 시작 실패의 원인은 애플리케이션 이벤트 로그에도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="87dbf-176">500.32 ANCM dll을 로드하지 못함</span><span class="sxs-lookup"><span data-stu-id="87dbf-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="87dbf-177">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-177">The worker process fails.</span></span> <span data-ttu-id="87dbf-178">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-178">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-179">이 오류의 가장 일반적인 원인은 앱이 호환되지 않는 프로세서 아키텍처에 대해 게시되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="87dbf-180">작업자 프로세스가 32비트 앱으로 실행 중이고 해당 앱이 대상 64 비트로 게시된 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="87dbf-181">이 오류를 해결하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-181">To fix this error, either:</span></span>

* <span data-ttu-id="87dbf-182">작업자 프로세스와 동일한 프로세서 아키텍처에 대해 앱을 다시 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="87dbf-183">앱을 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-executables-fde)로 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="87dbf-184">500.33 ANCM 요청 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="87dbf-185">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-185">The worker process fails.</span></span> <span data-ttu-id="87dbf-186">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-186">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-187">앱이 `Microsoft.AspNetCore.App` 프레임워크를 참조하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="87dbf-188">`Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는 앱만 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)에서 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="87dbf-189">이 오류를 해결하려면 앱이 `Microsoft.AspNetCore.App` 프레임워크를 대상으로 하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="87dbf-190">`.runtimeconfig.json`을 확인하여 앱이 대상으로 하는 프레임워크를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="87dbf-191">500.34 ANCM 혼합된 호스팅 모델이 지원되지 않음</span><span class="sxs-lookup"><span data-stu-id="87dbf-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="87dbf-192">작업자 프로세스는 동일한 프로세스에서 In Process 앱과 out-of-process 앱을 모두 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="87dbf-193">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="87dbf-194">500.35 ANCM 동일한 프로세스의 여러 In-Process 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="87dbf-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="87dbf-195">작업자 프로세스는 동일한 프로세스에서 여러 in-process 앱을 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="87dbf-196">이 오류를 해결하려면 별도의 IIS 애플리케이션 풀에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="87dbf-197">500.36 ANCM Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="87dbf-198">Out-of-process 요청 처리기, *aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 파일 옆에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="87dbf-199">이는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 손상된 설치를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="87dbf-200">이 오류를 해결하려면 [.NET Core 호스팅 번들](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)(IIS의 경우) 또는 Visual Studio(IIS Express의 경우)의 설치를 복구합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="87dbf-201">500.37 ANCM 시작 시간 제한 내에 시작하지 못함</span><span class="sxs-lookup"><span data-stu-id="87dbf-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="87dbf-202">ANCM은 제공된 시작 시간 제한 내에 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="87dbf-203">기본적으로 제한 시간은 120초입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="87dbf-204">이 오류는 동일한 머신에서 많은 수의 앱을 시작할 때 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="87dbf-205">시작하는 동안 서버에서 CPU/메모리 사용량이 급증하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="87dbf-206">여러 앱의 시작 프로세스를 분산해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="87dbf-207">500.38 ANCM 애플리케이션 DLL을 찾을 수 없음</span><span class="sxs-lookup"><span data-stu-id="87dbf-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="87dbf-208">ANCM에서 실행 파일 옆에 있어야 하는 애플리케이션 DLL을 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="87dbf-209">이 오류는 [단일 파일 실행 파일](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)로 패키지된 앱을 In-process 호스팅 모델을 사용하여 호스트할 때 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="87dbf-210">In-process 모델을 사용하려면 ANCM에서 .NET Core 앱을 기존 IIS 프로세스로 로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="87dbf-211">단일 파일 배포 모델에서는 이 시나리오가 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="87dbf-212">앱의 프로젝트 파일에서 다음 방법 중 **하나**를 사용하여 이 오류를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="87dbf-213">`PublishSingleFile` MSBuild 속성을 `false`로 설정하여 단일 파일 게시를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="87dbf-214">`AspNetCoreHostingModel` MSBuild 속성을 `OutOfProcess`로 설정하여 out-of-process 호스팅 모델로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="87dbf-215">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-215">502.5 Process Failure</span></span>

<span data-ttu-id="87dbf-216">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-216">The worker process fails.</span></span> <span data-ttu-id="87dbf-217">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-217">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-218">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="87dbf-219">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="87dbf-220">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="87dbf-221">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="87dbf-222">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="87dbf-223">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="87dbf-224">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="87dbf-225">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="87dbf-226">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="87dbf-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="87dbf-227">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="87dbf-228">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="87dbf-229">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="87dbf-230">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="87dbf-231">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="87dbf-232">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="87dbf-233">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="87dbf-234">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="87dbf-235">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="87dbf-236">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="87dbf-236">Connection reset</span></span>

<span data-ttu-id="87dbf-237">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="87dbf-238">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="87dbf-239">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="87dbf-240">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="87dbf-241">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="87dbf-241">Default startup limits</span></span>

<span data-ttu-id="87dbf-242">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit*가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="87dbf-243">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="87dbf-244">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="87dbf-245">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="87dbf-246">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-247">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="87dbf-248">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="87dbf-249">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="87dbf-250">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="87dbf-251">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="87dbf-252">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="87dbf-253">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="87dbf-254">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-255">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-256">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-257">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-258">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-259">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="87dbf-260">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-260">Examine the log.</span></span> <span data-ttu-id="87dbf-261">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="87dbf-262">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="87dbf-263">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-264">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="87dbf-265">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-266">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-267">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-268">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="87dbf-269">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="87dbf-270">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="87dbf-271">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-271">Run the app:</span></span>
   * <span data-ttu-id="87dbf-272">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="87dbf-273">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="87dbf-274">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-275">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-276">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-278">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-279">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="87dbf-280">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="87dbf-281">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-281">**Current release**</span></span>

* <span data-ttu-id="87dbf-282">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="87dbf-283">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="87dbf-284">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="87dbf-285">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="87dbf-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="87dbf-286">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-287">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-288">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-290">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-291">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="87dbf-292">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-293">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="87dbf-294">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-295">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-296">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="87dbf-297">**추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="87dbf-298">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-299">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-300">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-301">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-302">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-303">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-303">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-304">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-304">Return to the Azure portal.</span></span> <span data-ttu-id="87dbf-305">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-306">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-307">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-308">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-309">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-310">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="87dbf-311">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="87dbf-312">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-313">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="87dbf-314">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="87dbf-315">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-316">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="87dbf-317">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-318">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-319">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="87dbf-320">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="87dbf-321">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-322">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="87dbf-323">ASP.NET Core 모듈 디버그 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-324">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="87dbf-325">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-326">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="87dbf-327">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="87dbf-328">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-328">Redeploy the app.</span></span>
   * <span data-ttu-id="87dbf-329">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="87dbf-330">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-331">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-332">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="87dbf-333">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="87dbf-334">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-335">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="87dbf-336">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="87dbf-337">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="87dbf-338">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-339">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-340">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-341">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-342">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-343">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="87dbf-344">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="87dbf-345">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="87dbf-346">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="87dbf-347">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="87dbf-348">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="87dbf-349">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="87dbf-350">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-350">Save the file.</span></span>

<span data-ttu-id="87dbf-351">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-352">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-353">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-353">There's no limit on log file size.</span></span> <span data-ttu-id="87dbf-354">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="87dbf-355">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-356">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="87dbf-357">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="87dbf-358">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="87dbf-359">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-360">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="87dbf-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="87dbf-361">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="87dbf-361">Monitoring blades</span></span>

<span data-ttu-id="87dbf-362">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="87dbf-363">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="87dbf-364">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="87dbf-365">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="87dbf-366">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="87dbf-367">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="87dbf-368">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="87dbf-369">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="87dbf-370">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="87dbf-371">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="87dbf-372">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="87dbf-373">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="87dbf-374">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-375">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-376">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-377">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-378">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-379">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-380">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-381">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="87dbf-382">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="87dbf-383">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="87dbf-384">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="87dbf-385">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="87dbf-386">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="87dbf-387">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="87dbf-388">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-388">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-389">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="87dbf-390">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="87dbf-391">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="87dbf-392">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-393">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="87dbf-394">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="87dbf-395">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-396">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-397">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-398">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-399">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="87dbf-400">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="87dbf-401">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="87dbf-402">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="87dbf-403">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="87dbf-404">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="87dbf-405">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="87dbf-406">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="87dbf-407">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.  </span><span class="sxs-lookup"><span data-stu-id="87dbf-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="87dbf-408">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-408">Run the app at a command prompt</span></span>

<span data-ttu-id="87dbf-409">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-410">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="87dbf-411">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-411">Framework-dependent deployment</span></span>

<span data-ttu-id="87dbf-412">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="87dbf-413">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="87dbf-414">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="87dbf-415">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-416">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-417">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-418">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="87dbf-419">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-419">Self-contained deployment</span></span>

<span data-ttu-id="87dbf-420">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="87dbf-421">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="87dbf-422">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="87dbf-423">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-424">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-425">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-426">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="87dbf-427">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="87dbf-428">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-429">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-430">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="87dbf-431">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="87dbf-432">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-432">Edit the *web.config* file.</span></span> <span data-ttu-id="87dbf-433">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="87dbf-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="87dbf-434">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="87dbf-435">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="87dbf-436">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="87dbf-437">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="87dbf-438">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-439">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-439">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-440">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="87dbf-441">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="87dbf-442">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-442">Study the log for errors.</span></span>

<span data-ttu-id="87dbf-443">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-444">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-445">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-446">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-446">Save the file.</span></span>

<span data-ttu-id="87dbf-447">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-448">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-449">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-450">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-451">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="87dbf-452">ASP.NET Core 모듈 디버그 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="87dbf-453">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="87dbf-454">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="87dbf-455">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="87dbf-456">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="87dbf-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="87dbf-457">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="87dbf-458">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="87dbf-459">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="87dbf-460">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="87dbf-461">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="87dbf-462">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="87dbf-462">Obtain data from an app</span></span>

<span data-ttu-id="87dbf-463">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="87dbf-464">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="87dbf-465">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="87dbf-466">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="87dbf-467">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="87dbf-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="87dbf-468">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="87dbf-469">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="87dbf-470">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="87dbf-471">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-472">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="87dbf-473">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="87dbf-474">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="87dbf-475">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-476">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="87dbf-477">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="87dbf-478">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="87dbf-479">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-480">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="87dbf-481">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="87dbf-482">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="87dbf-483">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="87dbf-483">Analyze the dump</span></span>

<span data-ttu-id="87dbf-484">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="87dbf-485">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="87dbf-486">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-486">Clear package caches</span></span>

<span data-ttu-id="87dbf-487">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="87dbf-488">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="87dbf-489">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="87dbf-490">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="87dbf-491">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="87dbf-492">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="87dbf-493">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="87dbf-494">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="87dbf-495">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87dbf-496">추가 자료</span><span class="sxs-lookup"><span data-stu-id="87dbf-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="87dbf-497">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-497">Azure documentation</span></span>

* [<span data-ttu-id="87dbf-498">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="87dbf-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="87dbf-499">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="87dbf-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="87dbf-500">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="87dbf-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="87dbf-501">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="87dbf-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="87dbf-502">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="87dbf-503">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="87dbf-504">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-505">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="87dbf-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="87dbf-506">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="87dbf-506">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="87dbf-507">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="87dbf-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="87dbf-508">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="87dbf-509">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="87dbf-510">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="87dbf-511">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="87dbf-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="87dbf-512">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-512">Visual Studio Code documentation</span></span>

* <span data-ttu-id="87dbf-513">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="87dbf-513">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="87dbf-514">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="87dbf-515">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="87dbf-516">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="87dbf-517">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="87dbf-518">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="87dbf-519">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="87dbf-520">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="87dbf-521">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="87dbf-522">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="87dbf-523">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="87dbf-524">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="87dbf-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="87dbf-525">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="87dbf-526">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-526">App startup errors</span></span>

<span data-ttu-id="87dbf-527">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="87dbf-528">로컬에서 디버그할 때 발생하는 *502.5 - 프로세스 실패* 또는 *500.30 - 시작 실패*는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="87dbf-529">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="87dbf-529">403.14 Forbidden</span></span>

<span data-ttu-id="87dbf-530">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-530">The app fails to start.</span></span> <span data-ttu-id="87dbf-531">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="87dbf-532">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="87dbf-533">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-534">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-535">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="87dbf-536">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-536">Perform the following steps:</span></span>

1. <span data-ttu-id="87dbf-537">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-538">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="87dbf-539">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="87dbf-540">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="87dbf-541">IIS에서 앱을 호스트하는 경우 **IIS 관리자**의 **기본 설정**에 표시되는 IIS **실제 경로**에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="87dbf-542">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="87dbf-543">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="87dbf-544">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="87dbf-545">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-545">500 Internal Server Error</span></span>

<span data-ttu-id="87dbf-546">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="87dbf-547">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="87dbf-548">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="87dbf-549">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="87dbf-550">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="87dbf-551">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="87dbf-552">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="87dbf-553">500.0 In-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="87dbf-554">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-554">The worker process fails.</span></span> <span data-ttu-id="87dbf-555">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-555">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-556">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 .NET Core CLR을 찾지 못하고 in-process 요청 처리기(*aspnetcorev2_inprocess.dll*)를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="87dbf-557">다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-557">Check that:</span></span>

* <span data-ttu-id="87dbf-558">앱은 [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet 패키지 또는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app) 중 하나를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="87dbf-559">앱이 대상으로 하는 ASP.NET Core 공유 프레임워크의 버전이 대상 머신에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="87dbf-560">500.0 Out-Of-Process 처리기 로드 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="87dbf-561">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-561">The worker process fails.</span></span> <span data-ttu-id="87dbf-562">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-562">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-563">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 out-of-process 호스팅 요청 처리기를 찾지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="87dbf-564">*aspnetcorev2_outofprocess.dll*이 *aspnetcorev2.dll* 옆의 하위 폴더에 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="87dbf-565">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-565">502.5 Process Failure</span></span>

<span data-ttu-id="87dbf-566">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-566">The worker process fails.</span></span> <span data-ttu-id="87dbf-567">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-567">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-568">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="87dbf-569">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="87dbf-570">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="87dbf-571">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="87dbf-572">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="87dbf-573">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="87dbf-574">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="87dbf-575">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="87dbf-576">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="87dbf-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="87dbf-577">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="87dbf-578">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="87dbf-579">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="87dbf-580">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="87dbf-581">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="87dbf-582">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="87dbf-583">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="87dbf-584">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="87dbf-585">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="87dbf-586">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="87dbf-586">Connection reset</span></span>

<span data-ttu-id="87dbf-587">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="87dbf-588">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="87dbf-589">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="87dbf-590">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="87dbf-591">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="87dbf-591">Default startup limits</span></span>

<span data-ttu-id="87dbf-592">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit*가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="87dbf-593">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="87dbf-594">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="87dbf-595">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="87dbf-596">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-597">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="87dbf-598">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="87dbf-599">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="87dbf-600">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="87dbf-601">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="87dbf-602">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="87dbf-603">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="87dbf-604">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-605">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-606">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-607">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-608">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-609">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="87dbf-610">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-610">Examine the log.</span></span> <span data-ttu-id="87dbf-611">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="87dbf-612">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="87dbf-613">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-614">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="87dbf-615">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-616">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-617">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-618">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="87dbf-619">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="87dbf-620">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="87dbf-621">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-621">Run the app:</span></span>
   * <span data-ttu-id="87dbf-622">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="87dbf-623">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="87dbf-624">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-625">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-626">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-628">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-629">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="87dbf-630">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="87dbf-631">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-631">**Current release**</span></span>

* <span data-ttu-id="87dbf-632">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="87dbf-633">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="87dbf-634">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="87dbf-635">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="87dbf-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="87dbf-636">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-637">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-638">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-640">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-641">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="87dbf-642">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-643">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="87dbf-644">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-645">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-646">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="87dbf-647">**추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="87dbf-648">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-649">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-650">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-651">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-652">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-653">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-653">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-654">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-654">Return to the Azure portal.</span></span> <span data-ttu-id="87dbf-655">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-656">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-657">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-658">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-659">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-660">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="87dbf-661">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="87dbf-662">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-663">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="87dbf-664">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="87dbf-665">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-666">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="87dbf-667">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-668">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-669">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="87dbf-670">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="87dbf-671">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-672">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="87dbf-673">ASP.NET Core 모듈 디버그 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-674">ASP.NET Core 모듈 디버그 로그는 ASP.NET Core 모듈에서 추가로 심층적인 로깅을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="87dbf-675">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-676">개선된 진단 로그를 사용하도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="87dbf-677">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)의 지침에 따라 개선된 진단 로깅을 위한 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="87dbf-678">앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-678">Redeploy the app.</span></span>
   * <span data-ttu-id="87dbf-679">Kudu 콘솔을 사용하여 [개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시되는 `<handlerSettings>`를 라이브 앱의 *web.config* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="87dbf-680">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-681">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-682">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="87dbf-683">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="87dbf-684">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-685">연필 단추를 선택하여 *web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="87dbf-686">[개선된 진단 로그](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 표시된 대로 `<handlerSettings>` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="87dbf-687">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="87dbf-688">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-689">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-690">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-691">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-692">폴더를 **site** > **wwwroot** 경로로 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-693">*aspnetcore debug.log* 파일에 대한 경로를 제공하지 않는 경우 파일이 목록에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="87dbf-694">경로를 제공한 경우 로그 파일의 위치로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="87dbf-695">파일 이름 옆의 연필 단추를 사용하여 로그 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="87dbf-696">문제 해결이 완료되면 디버그 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="87dbf-697">향상된 디버그 로그를 사용하지 않도록 설정하려면 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="87dbf-698">*web.config* 파일에서 `<handlerSettings>`를 로컬에서 제거하고 앱을 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="87dbf-699">Kudu 콘솔을 사용하여 *web.config* 파일을 편집하고 `<handlerSettings>` 섹션을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="87dbf-700">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-700">Save the file.</span></span>

<span data-ttu-id="87dbf-701">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-702">디버그 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-703">로그 파일 크기에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-703">There's no limit on log file size.</span></span> <span data-ttu-id="87dbf-704">앱 시작 문제를 해결하려면 디버그 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="87dbf-705">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-706">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="87dbf-707">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="87dbf-708">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="87dbf-709">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-710">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="87dbf-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="87dbf-711">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="87dbf-711">Monitoring blades</span></span>

<span data-ttu-id="87dbf-712">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="87dbf-713">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="87dbf-714">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="87dbf-715">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="87dbf-716">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="87dbf-717">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="87dbf-718">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="87dbf-719">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="87dbf-720">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="87dbf-721">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="87dbf-722">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="87dbf-723">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="87dbf-724">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-725">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-726">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-727">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-728">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-729">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-730">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-731">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="87dbf-732">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="87dbf-733">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="87dbf-734">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="87dbf-735">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="87dbf-736">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="87dbf-737">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="87dbf-738">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-738">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-739">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="87dbf-740">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="87dbf-741">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="87dbf-742">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-743">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="87dbf-744">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="87dbf-745">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-746">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-747">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-748">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-749">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="87dbf-750">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="87dbf-751">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="87dbf-752">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="87dbf-753">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="87dbf-754">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="87dbf-755">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="87dbf-756">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="87dbf-757">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.  </span><span class="sxs-lookup"><span data-stu-id="87dbf-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="87dbf-758">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-758">Run the app at a command prompt</span></span>

<span data-ttu-id="87dbf-759">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-760">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="87dbf-761">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-761">Framework-dependent deployment</span></span>

<span data-ttu-id="87dbf-762">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="87dbf-763">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="87dbf-764">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="87dbf-765">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-766">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-767">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-768">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="87dbf-769">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-769">Self-contained deployment</span></span>

<span data-ttu-id="87dbf-770">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="87dbf-771">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="87dbf-772">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="87dbf-773">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-774">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-775">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-776">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="87dbf-777">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="87dbf-778">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-779">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-780">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="87dbf-781">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="87dbf-782">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-782">Edit the *web.config* file.</span></span> <span data-ttu-id="87dbf-783">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="87dbf-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="87dbf-784">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="87dbf-785">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="87dbf-786">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="87dbf-787">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="87dbf-788">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-789">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-789">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-790">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="87dbf-791">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="87dbf-792">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-792">Study the log for errors.</span></span>

<span data-ttu-id="87dbf-793">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-794">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-795">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-796">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-796">Save the file.</span></span>

<span data-ttu-id="87dbf-797">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-798">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-799">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-800">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-801">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="87dbf-802">ASP.NET Core 모듈 디버그 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="87dbf-803">앱의 *web.config* 파일에 다음 처리기 설정을 추가하여 ASP.NET Core 모듈 디버그 로그를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="87dbf-804">로그에 대해 지정된 경로가 있는지 및 앱 풀의 ID에 해당 위치에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="87dbf-805">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="87dbf-806">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="87dbf-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="87dbf-807">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="87dbf-808">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="87dbf-809">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="87dbf-810">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="87dbf-811">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="87dbf-812">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="87dbf-812">Obtain data from an app</span></span>

<span data-ttu-id="87dbf-813">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="87dbf-814">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="87dbf-815">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="87dbf-816">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="87dbf-817">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="87dbf-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="87dbf-818">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="87dbf-819">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="87dbf-820">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="87dbf-821">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-822">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="87dbf-823">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="87dbf-824">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="87dbf-825">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-826">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="87dbf-827">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="87dbf-828">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="87dbf-829">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-830">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="87dbf-831">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="87dbf-832">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="87dbf-833">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="87dbf-833">Analyze the dump</span></span>

<span data-ttu-id="87dbf-834">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="87dbf-835">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="87dbf-836">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-836">Clear package caches</span></span>

<span data-ttu-id="87dbf-837">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="87dbf-838">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="87dbf-839">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="87dbf-840">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="87dbf-841">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="87dbf-842">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="87dbf-843">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="87dbf-844">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="87dbf-845">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87dbf-846">추가 자료</span><span class="sxs-lookup"><span data-stu-id="87dbf-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="87dbf-847">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-847">Azure documentation</span></span>

* [<span data-ttu-id="87dbf-848">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="87dbf-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="87dbf-849">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="87dbf-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="87dbf-850">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="87dbf-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="87dbf-851">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="87dbf-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="87dbf-852">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="87dbf-853">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="87dbf-854">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-855">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="87dbf-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="87dbf-856">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="87dbf-856">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="87dbf-857">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="87dbf-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="87dbf-858">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="87dbf-859">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="87dbf-860">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="87dbf-861">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="87dbf-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="87dbf-862">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-862">Visual Studio Code documentation</span></span>

* <span data-ttu-id="87dbf-863">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="87dbf-863">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="87dbf-864">이 문서에서는 앱이 Azure App Service 또는 IIS에 배포될 때 일반적인 앱 시작 오류에 대한 정보와 오류 진단 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="87dbf-865">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="87dbf-866">일반적인 시작 HTTP 상태 코드 시나리오에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="87dbf-867">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="87dbf-868">Azure App Service에 배포된 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="87dbf-869">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="87dbf-870">IIS에 배포되었거나 IIS Express에서 로컬로 실행되는 앱에 대한 문제 해결 조언을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="87dbf-871">이 지침은 Windows Server 및 Windows 데스크톱 배포 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="87dbf-872">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="87dbf-873">주요 업그레이드를 수행하거나 패키지 버전을 변경할 때 일관되지 않은 패키지가 앱을 중단시킬 경우에 수행할 작업을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="87dbf-874">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="87dbf-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="87dbf-875">추가 문제 해결 항목을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="87dbf-876">앱 시작 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-876">App startup errors</span></span>

<span data-ttu-id="87dbf-877">Visual Studio에서 ASP.NET Core 프로젝트는 기본적으로 디버그 중에 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 호스팅으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="87dbf-878">로컬에서 디버그할 때 발생하는 *502.5 프로세스 실패*는 이 항목의 권장 사항을 사용하여 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="87dbf-879">403.14 사용할 수 없음</span><span class="sxs-lookup"><span data-stu-id="87dbf-879">403.14 Forbidden</span></span>

<span data-ttu-id="87dbf-880">앱이 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-880">The app fails to start.</span></span> <span data-ttu-id="87dbf-881">다음 오류가 로깅됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="87dbf-882">이 오류는 일반적으로 다음과 같은 시나리오를 포함하여 호스팅 시스템의 중단된 배포로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="87dbf-883">앱이 호스팅 시스템의 잘못된 폴더에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-884">배포 프로세스에서 앱의 모든 파일 및 폴더를 호스팅 시스템의 배포 폴더로 이동하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="87dbf-885">*web.config* 파일이 배포에 없거나 *web.config* 파일 내용의 형식이 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="87dbf-886">다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-886">Perform the following steps:</span></span>

1. <span data-ttu-id="87dbf-887">호스팅 시스템의 배포 폴더에서 모든 파일과 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-888">Visual Studio, PowerShell 또는 수동 배포와 같은 일반적인 배포 방법을 사용하여 앱의 *publish* 폴더 콘텐츠를 호스팅 시스템에 다시 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="87dbf-889">*web.config* 파일이 배포에 있고 내용이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="87dbf-890">Azure App Service에 호스트할 때 앱이 `D:\home\site\wwwroot` 폴더에 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="87dbf-891">IIS에서 앱을 호스트하는 경우 **IIS 관리자**의 **기본 설정**에 표시되는 IIS **실제 경로**에 앱이 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="87dbf-892">호스팅 시스템의 배포를 프로젝트의 *publish* 폴더의 콘텐츠와 비교하여 모든 앱의 파일 및 폴더가 배포되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="87dbf-893">게시된 ASP.NET Core 앱의 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="87dbf-894">*web.config* 파일에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="87dbf-895">500 내부 서버 오류</span><span class="sxs-lookup"><span data-stu-id="87dbf-895">500 Internal Server Error</span></span>

<span data-ttu-id="87dbf-896">앱이 시작되지만 오류로 인해 서버에서 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="87dbf-897">이 오류는 시작하는 동안 또는 응답을 만드는 동안 앱 코드 내에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="87dbf-898">응답에 콘텐츠가 없거나 응답이 브라우저에 ‘500 내부 서버 오류’로 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="87dbf-899">애플리케이션 이벤트 로그는 일반적으로 앱이 정상적으로 시작되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="87dbf-900">서버의 관점에서 보면 맞습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="87dbf-901">앱이 시작되었지만 유효한 응답을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="87dbf-902">서버의 명령 프롬프트에서 앱을 실행하거나 ASP.NET Core 모듈 stdout 로그를 사용하여 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="87dbf-903">502.5 프로세스 실패</span><span class="sxs-lookup"><span data-stu-id="87dbf-903">502.5 Process Failure</span></span>

<span data-ttu-id="87dbf-904">작업자 프로세스가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-904">The worker process fails.</span></span> <span data-ttu-id="87dbf-905">앱이 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-905">The app doesn't start.</span></span>

<span data-ttu-id="87dbf-906">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 작업자 프로세스를 시작하려고 하지만 시작할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="87dbf-907">프로세스 시작 실패의 원인은 일반적으로 애플리케이션 이벤트 로그 및 ASP.NET Core 모듈 stdout 로그의 항목에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="87dbf-908">일반적인 실패 조건은 존재하지 않는 ASP.NET Core 공유 프레임워크의 버전을 대상으로 하여 앱이 잘못 구성되었다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="87dbf-909">대상 머신에 설치된 ASP.NET Core 공유 프레임워크의 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="87dbf-910">공유 프레임워크는 머신에 설치되고 메타패키지(예: `Microsoft.AspNetCore.App`)에서 참조되는 어셈블리( *.dll* 파일) 세트입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="87dbf-911">메타패키지 참조는 필요한 최소 버전을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="87dbf-912">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="87dbf-913">호스팅 또는 앱의 잘못된 구성으로 인해 작업자 프로세스가 실패하면 ‘502.5 프로세스 실패’ 오류 페이지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="87dbf-914">애플리케이션을 시작하지 못함(오류 코드 '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="87dbf-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="87dbf-915">앱의 어셈블리( *.dll*)를 로드할 수 없기 때문에 앱을 시작하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="87dbf-916">게시된 앱과 w3wp/iisexpress 프로세스 간에 비트 수가 불일치하는 경우 이 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="87dbf-917">앱 풀의 32비트 설정이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="87dbf-918">IIS 관리자의 **애플리케이션 풀**에서 앱 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="87dbf-919">**작업** 패널의 **애플리케이션 풀 편집**에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="87dbf-920">**32비트 애플리케이션 사용**을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="87dbf-921">32비트(x86) 앱을 배포하는 경우 값을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="87dbf-922">64비트(x64) 앱을 배포하는 경우 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="87dbf-923">프로젝트 파일의 `<Platform>` MSBuild 속성과 앱의 게시된 비트 간에 충돌이 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="87dbf-924">연결 다시 설정</span><span class="sxs-lookup"><span data-stu-id="87dbf-924">Connection reset</span></span>

<span data-ttu-id="87dbf-925">헤더가 전송된 후 오류가 발생할 경우, 오류가 발생할 때 서버에서 **500 내부 서버 오류**를 전송하는 것은 너무 늦은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="87dbf-926">응답에 대한 복잡한 개체의 serialization 중에 오류가 발생할 때 이 문제가 종종 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="87dbf-927">이 유형의 오류는 클라이언트에서 ‘연결 다시 설정’ 오류로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="87dbf-928">[애플리케이션 로깅](xref:fundamentals/logging/index)은 이러한 유형의 오류를 해결하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="87dbf-929">기본 시작 제한</span><span class="sxs-lookup"><span data-stu-id="87dbf-929">Default startup limits</span></span>

<span data-ttu-id="87dbf-930">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)은 기본 *startupTimeLimit*가 120초로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="87dbf-931">기본값으로 남아 있으면 앱에서 모듈이 프로세스 실패를 기록하기 전에 시작하는 데 최대 2분이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="87dbf-932">모듈 구성에 대한 자세한 내용은 [aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="87dbf-933">Azure App Service 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="87dbf-934">애플리케이션 이벤트 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-935">애플리케이션 이벤트 로그에 액세스하려면 Azure Portal에서 **문제 진단 및 해결** 블레이드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="87dbf-936">Azure Portal에서 **Azure App Services**의 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="87dbf-937">**문제 진단 및 해결**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="87dbf-938">**진단 도구** 제목을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="87dbf-939">**지원 도구**에서 **애플리케이션 이벤트** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="87dbf-940">**원본** 열에서 *IIS AspNetCoreModule* 또는 *IIS AspNetCoreModule V2* 항목이 제공하는 최신 오류를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="87dbf-941">**문제 진단 및 해결** 블레이드를 사용하는 대신에 [Kudu](https://github.com/projectkudu/kudu/wiki)를 사용하여 애플리케이션 이벤트 로그 파일을 직접 검토하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="87dbf-942">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-943">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-944">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-945">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-946">**LogFiles** 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-947">*eventlog.xml* 파일 옆에 있는 연필 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="87dbf-948">로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-948">Examine the log.</span></span> <span data-ttu-id="87dbf-949">로그 아래쪽으로 스크롤하여 가장 최근 이벤트를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="87dbf-950">Kudu 콘솔에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="87dbf-951">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-952">[Kudu](https://github.com/projectkudu/kudu/wiki) 원격 실행 콘솔에서 앱을 실행하여 오류를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="87dbf-953">**개발 도구** 영역에서 **고급 도구**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="87dbf-954">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-955">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-956">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="87dbf-957">32비트(x86) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="87dbf-958">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="87dbf-959">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-959">Run the app:</span></span>
   * <span data-ttu-id="87dbf-960">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="87dbf-961">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="87dbf-962">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-963">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-964">ASP.NET Core {VERSION}(x86) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-966">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-967">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="87dbf-968">64비트(x64) 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="87dbf-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="87dbf-969">**현재 릴리스**</span><span class="sxs-lookup"><span data-stu-id="87dbf-969">**Current release**</span></span>

* <span data-ttu-id="87dbf-970">앱이 64비트(x64) [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="87dbf-971">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="87dbf-972">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="87dbf-973">앱 실행: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="87dbf-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="87dbf-974">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="87dbf-975">**미리 보기 릴리스에서 실행되는 프레임워크 종속 배포**</span><span class="sxs-lookup"><span data-stu-id="87dbf-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="87dbf-976">ASP.NET Core {VERSION}(x64) 런타임 사이트 확장을 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="87dbf-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}`는 런타임 버전임)</span><span class="sxs-lookup"><span data-stu-id="87dbf-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="87dbf-978">앱 실행: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="87dbf-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="87dbf-979">오류를 표시하는 앱의 콘솔 출력이 Kudu 콘솔에 파이프됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="87dbf-980">ASP.NET Core 모듈 stdout 로그(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="87dbf-981">ASP.NET Core 모듈 stdout 로그는 종종 애플리케이션 이벤트 로그에서 찾을 수 없는 유용한 오류 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="87dbf-982">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-983">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-984">**문제 범주 선택** 아래에서 **웹앱 작동 중단** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="87dbf-985">**추천 솔루션**> **Stdout 로그 리디렉션 사용** 아래에서 **Kudu 콘솔을 열어 Web.Config 편집** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="87dbf-986">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="87dbf-987">아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-988">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-989">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-990">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-991">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-991">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-992">Azure Portal로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-992">Return to the Azure portal.</span></span> <span data-ttu-id="87dbf-993">**개발 도구** 영역에서 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-994">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-995">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-996">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-997">**LogFiles** 폴더를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="87dbf-998">**수정됨** 열을 검사하고 연필 아이콘을 선택하여 최신 수정 날짜가 있는 stdout 로그를 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="87dbf-999">로그 파일이 열리면 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="87dbf-1000">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-1001">Kudu **진단 콘솔**에서 **site** > **wwwroot** 경로로 돌아가서 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="87dbf-1002">연필 아이콘을 선택하여 **web.config** 파일을 다시 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="87dbf-1003">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-1004">**저장**을 선택하여 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="87dbf-1005">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-1006">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-1007">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="87dbf-1008">앱 시작 문제를 해결하려면 stdout 로깅만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="87dbf-1009">시작 후 ASP.NET Core 앱의 일반적인 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-1010">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="87dbf-1011">느리거나 중단된 앱(Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="87dbf-1012">요청 시 앱이 느리게 응답하거나 중단되면 다음 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="87dbf-1013">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-1014">Crash Diagnoser 사이트 확장을 사용하여 Azure 웹앱에서 일시적인 예외 문제 또는 성능 문제에 대한 덤프 캡처</span><span class="sxs-lookup"><span data-stu-id="87dbf-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="87dbf-1015">모니터링 블레이드</span><span class="sxs-lookup"><span data-stu-id="87dbf-1015">Monitoring blades</span></span>

<span data-ttu-id="87dbf-1016">모니터링 블레이드는 이 항목의 앞부분에서 설명된 방법에 대한 대체 문제 해결 환경을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="87dbf-1017">이 블레이드를 사용하여 500 시리즈 오류를 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="87dbf-1018">ASP.NET Core 확장이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="87dbf-1019">확장이 설치되어 있지 않으면 수동으로 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="87dbf-1020">**개발 도구** 블레이드 섹션에서 **확장** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="87dbf-1021">**ASP.NET Core 확장**이 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="87dbf-1022">확장이 설치되어 있지 않으면 **추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="87dbf-1023">목록에서 **ASP.NET Core 확장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="87dbf-1024">**확인**을 선택하여 적합한 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="87dbf-1025">**확장 추가** 블레이드에서 **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="87dbf-1026">정보 팝업 메시지는 확장이 성공적으로 설치되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="87dbf-1027">stdout 로깅을 사용할 수 없는 경우 다음 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="87dbf-1028">Azure Portal에서 **개발 도구** 영역의 **고급 도구** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="87dbf-1029">**이동&rarr;** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="87dbf-1030">새 브라우저 탭 또는 창에서 Kudu 콘솔이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="87dbf-1031">페이지 위쪽의 탐색 모음을 사용하여 **디버그 콘솔**을 열고 **CMD**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="87dbf-1032">**site** > **wwwroot** 경로로 폴더를 열고 아래로 스크롤하여 목록 아래쪽에 있는 *web.config* 파일을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="87dbf-1033">*web.config* 파일 옆에 있는 연필 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-1034">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로를 `\\?\%home%\LogFiles\stdout`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="87dbf-1035">**저장**을 선택하여 업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="87dbf-1036">계속해서 진단 로깅을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="87dbf-1037">Azure Portal에서 **진단 로그** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="87dbf-1038">**애플리케이션 로깅(파일 시스템)** 및 **자세한 오류 메시지**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="87dbf-1039">블레이드 위쪽에 있는 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="87dbf-1040">FREB(실패한 요청 이벤트 버퍼링) 로깅이라고도 하는 실패한 요청 추적을 포함하려면 **실패한 요청 추적**에 대해 **켜기** 스위치를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="87dbf-1041">포털의 **진단 로그** 블레이드 바로 아래에 나열된 **로그 스트림** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="87dbf-1042">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-1043">로그 스트림 데이터 내에 오류의 원인이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="87dbf-1044">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="87dbf-1045">실패한 요청 추적 로그(FREB 로그)를 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="87dbf-1046">Azure Portal에서 **문제 진단 및 해결** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="87dbf-1047">사이드바의 **지원 도구** 영역에서 **실패한 요청 추적 로그**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="87dbf-1048">자세한 내용은 [Azure App Service 에서 웹앱에 대한 진단 로깅 사용 항목의 실패한 요청 추적 섹션](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) 및 [Azure의 웹앱에 대한 애플리케이션 성능 FAQ를 참조하세요. 실패한 요청 추적을 켜려면 어떻게 해야 하나요?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="87dbf-1049">자세한 내용은 [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-1050">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-1051">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-1052">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-1053">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="87dbf-1054">IIS에 대한 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="87dbf-1055">애플리케이션 이벤트 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="87dbf-1056">애플리케이션 이벤트 로그에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="87dbf-1057">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="87dbf-1058">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="87dbf-1059">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="87dbf-1060">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="87dbf-1061">오류는 ‘소스’ 열에서 ‘IIS AspNetCore 모듈’ 또는 ‘IIS Express AspNetCore 모듈’의 값을 포함합니다.  </span><span class="sxs-lookup"><span data-stu-id="87dbf-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="87dbf-1062">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="87dbf-1063">애플리케이션 이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="87dbf-1064">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="87dbf-1065">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="87dbf-1066">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="87dbf-1067">명령 프롬프트에서 배포 폴더로 이동하고 *dotnet.exe*로 앱의 어셈블리를 실행하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="87dbf-1068">`dotnet .\<assembly_name>.dll` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="87dbf-1069">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-1070">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-1071">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-1072">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="87dbf-1073">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="87dbf-1073">Self-contained deployment</span></span>

<span data-ttu-id="87dbf-1074">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="87dbf-1075">명령 프롬프트에서 배포 폴더로 이동하고 앱의 실행 파일을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="87dbf-1076">`<assembly_name>.exe` 명령에서 \<assembly_name>을 앱 어셈블리의 이름으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="87dbf-1077">오류를 표시하는 앱의 콘솔 출력이 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="87dbf-1078">앱에 대한 요청을 실행할 때 오류가 발생하는 경우에는 Kestrel이 수신 대기하는 호스트 및 포트에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="87dbf-1079">기본 호스트 및 게시를 사용하여 `http://localhost:5000/`에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="87dbf-1080">앱이 Kestrel 엔드포인트 주소에서 정상적으로 응답하는 경우 문제는 호스팅 구성과 관련이 있으며 앱 내에서 관련되었을 가능성은 작습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="87dbf-1081">ASP.NET Core 모듈 stdout 로그(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="87dbf-1082">stdout 로그를 사용하고 보려면:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="87dbf-1083">호스팅 시스템에서 사이트의 배포 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="87dbf-1084">*logs* 폴더가 없으면 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="87dbf-1085">MSBuild를 사용하여 배포에서 *logs* 폴더를 자동으로 만드는 방법에 대한 지침은 [디렉터리 구조](xref:host-and-deploy/directory-structure) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="87dbf-1086">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="87dbf-1087">**stdoutLogEnabled**를 `true`로 설정하고 **stdoutLogFile** 경로가 *logs* 폴더를 가리키도록 변경합니다(예: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="87dbf-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="87dbf-1088">경로의 `stdout`은 로그 파일 이름 접두사입니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="87dbf-1089">로그가 만들어질 때 타임스탬프, 프로세스 ID 및 파일 확장명이 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="87dbf-1090">`stdout`을 파일 이름 접두사로 사용하여 일반적인 로그 파일의 이름은 *stdout_20180205184032_5412.log*로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="87dbf-1091">애플리케이션 풀의 ID에 *로그* 폴더에 대한 쓰기 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="87dbf-1092">업데이트된 *web.config* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-1093">앱에 대한 요청을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="87dbf-1094">*logs* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="87dbf-1095">가장 최근의 stdout 로그를 찾아서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="87dbf-1096">오류에 대한 로그를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1096">Study the log for errors.</span></span>

<span data-ttu-id="87dbf-1097">문제 해결이 완료되면 stdout 로깅을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="87dbf-1098">*web.config* 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="87dbf-1099">**stdoutLogEnabled**를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="87dbf-1100">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1100">Save the file.</span></span>

<span data-ttu-id="87dbf-1101">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-1102">stdout 로그를 사용하지 않도록 설정하지 않으면 앱 또는 서버 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="87dbf-1103">로그 파일 크기 또는 생성되는 로그 파일 수에 대한 제한은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="87dbf-1104">ASP.NET Core 앱의 루틴 로깅에는 로그 파일 크기를 제한하고 로그를 회전하는 로깅 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="87dbf-1105">자세한 내용은 [타사 로깅 공급자](xref:fundamentals/logging/index#third-party-logging-providers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="87dbf-1106">개발자 예외 페이지 사용</span><span class="sxs-lookup"><span data-stu-id="87dbf-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="87dbf-1107">`ASPNETCORE_ENVIRONMENT` [ 환경 변수를 web.config에 추가](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)하여 개발 환경에서 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="87dbf-1108">앱 시작 시 호스트 작성기의 `UseEnvironment`에 의해 환경이 재정의되지 않는 한, 환경 변수를 설정하면 앱이 실행될 때 [개발자 예외 페이지](xref:fundamentals/error-handling)가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="87dbf-1109">`ASPNETCORE_ENVIRONMENT`에 대한 환경 변수 설정은 인터넷에 노출되지 않은 스테이징 및 테스트 서버에서만 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="87dbf-1110">문제를 해결한 후 *web.config* 파일에서 환경 변수를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="87dbf-1111">*web.config*에서 환경 변수를 설정하는 방법에 대한 자세한 내용은[aspNetCore의 environmentVariables 자식 요소](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="87dbf-1112">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="87dbf-1112">Obtain data from an app</span></span>

<span data-ttu-id="87dbf-1113">앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="87dbf-1114">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="87dbf-1115">앱이 느리거나 중단됨(IIS)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="87dbf-1116">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="87dbf-1117">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="87dbf-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="87dbf-1118">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="87dbf-1119">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="87dbf-1120">앱 풀에는 폴더에 대한 쓰기 액세스 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="87dbf-1121">[EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-1122">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="87dbf-1123">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="87dbf-1124">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="87dbf-1125">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="87dbf-1126">앱에서 [in-process 호스팅 모델](xref:host-and-deploy/iis/index#in-process-hosting-model)을 사용하는 경우 *w3wp.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="87dbf-1127">앱에서 [out-of-process 호스팅 모델](xref:host-and-deploy/iis/index#out-of-process-hosting-model)을 사용하는 경우 *dotnet.exe*에 대한 스크립트 실행:</span><span class="sxs-lookup"><span data-stu-id="87dbf-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="87dbf-1128">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="87dbf-1129">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="87dbf-1130">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="87dbf-1131">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="87dbf-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="87dbf-1132">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="87dbf-1133">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="87dbf-1133">Analyze the dump</span></span>

<span data-ttu-id="87dbf-1134">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="87dbf-1135">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="87dbf-1136">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="87dbf-1136">Clear package caches</span></span>

<span data-ttu-id="87dbf-1137">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="87dbf-1138">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="87dbf-1139">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="87dbf-1140">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="87dbf-1141">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="87dbf-1142">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="87dbf-1143">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="87dbf-1144">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="87dbf-1145">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="87dbf-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87dbf-1146">추가 자료</span><span class="sxs-lookup"><span data-stu-id="87dbf-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="87dbf-1147">Azure 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-1147">Azure documentation</span></span>

* [<span data-ttu-id="87dbf-1148">ASP.NET Core용 Application Insights</span><span class="sxs-lookup"><span data-stu-id="87dbf-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="87dbf-1149">Visual Studio를 사용하여 Azure App Service에서 웹앱 문제 해결의 원격 디버깅 웹앱 섹션</span><span class="sxs-lookup"><span data-stu-id="87dbf-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="87dbf-1150">Azure App Service 진단 개요</span><span class="sxs-lookup"><span data-stu-id="87dbf-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="87dbf-1151">방법: Azure App Service에서 앱 모니터링</span><span class="sxs-lookup"><span data-stu-id="87dbf-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="87dbf-1152">Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="87dbf-1153">Azure 웹앱에서 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음”의 HTTP 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="87dbf-1154">Azure App Service에서 느린 웹앱 성능 문제 해결</span><span class="sxs-lookup"><span data-stu-id="87dbf-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="87dbf-1155">Azure의 웹앱에 대한 애플리케이션 성능 FAQ</span><span class="sxs-lookup"><span data-stu-id="87dbf-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* <span data-ttu-id="87dbf-1156">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)(Azure 웹앱 샌드박스(App Service 런타임 실행 제한))</span><span class="sxs-lookup"><span data-stu-id="87dbf-1156">[Azure Web App sandbox (App Service runtime execution limitations)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)</span></span>
* [<span data-ttu-id="87dbf-1157">Azure Friday: Azure App Service 진단 및 문제 해결 환경(12분 동영상)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="87dbf-1158">Visual Studio 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="87dbf-1159">Visual Studio 2017의 Azure에서 IIS의 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="87dbf-1160">Visual Studio 2017의 원격 IIS 컴퓨터에서 원격 디버그 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87dbf-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="87dbf-1161">Visual Studio를 사용하여 디버깅하는 자세한 내용</span><span class="sxs-lookup"><span data-stu-id="87dbf-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="87dbf-1162">Visual Studio Code 설명서</span><span class="sxs-lookup"><span data-stu-id="87dbf-1162">Visual Studio Code documentation</span></span>

* <span data-ttu-id="87dbf-1163">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)(Visual Studio Code를 사용한 디버깅)</span><span class="sxs-lookup"><span data-stu-id="87dbf-1163">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)</span></span>

::: moniker-end
