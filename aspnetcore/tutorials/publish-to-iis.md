---
title: IIS에 ASP.NET Core 앱 게시
author: guardrex
description: IIS 서버에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: c31beae16f46153daac188ab1638e5530584ac88
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927093"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="e2c68-103">IIS에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="e2c68-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="e2c68-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="e2c68-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e2c68-105">이 자습서에서는 IIS 서버에서 ASP.NET Core 앱을 호스트하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="e2c68-106">이 자습서에서 다루는 주제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e2c68-107">Windows Server에 .NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e2c68-108">IIS 관리자에서 IIS 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e2c68-109">ASP.NET Core 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2c68-110">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e2c68-110">Prerequisites</span></span>

* <span data-ttu-id="e2c68-111">[.NET Core SDK](/dotnet/core/sdk)가 개발 머신에 설치되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="e2c68-112">Windows Server가 **웹 서버(IIS)** 서버 역할로 구성되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="e2c68-113">서버가 IIS를 사용하여 웹 사이트를 호스트하도록 구성되지 않은 경우 <xref:host-and-deploy/iis/index#iis-configuration> 문서의 ‘IIS 구성’ 섹션에 있는 지침을 따르고 이 자습서로 돌아옵니다. </span><span class="sxs-lookup"><span data-stu-id="e2c68-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="e2c68-114">**IIS 구성 및 웹 사이트 보안에는 이 자습서에서 다루지 않는 개념이 포함됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e2c68-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="e2c68-115">IIS에서 프로덕션 앱을 호스트하기 전에 [Microsoft IIS 설명서](https://www.iis.net/)의 IIS 지침 및 [IIS를 사용한 호스트에 대한 ASP.NET Core 문서](xref:host-and-deploy/iis/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2c68-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="e2c68-116">이 자습서에서 다루지 않는 IIS 호스팅에 대한 중요한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="e2c68-117">ASP.NET Core 데이터 보호에 대한 레지스트리 하이브 만들기</span><span class="sxs-lookup"><span data-stu-id="e2c68-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="e2c68-118">앱 풀의 ACL(액세스 제어 목록) 구성</span><span class="sxs-lookup"><span data-stu-id="e2c68-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="e2c68-119">IIS 배포 개념에 중점을 두기 위해 이 자습서에서는 IIS에서 HTTPS 보안을 구성하지 않고 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="e2c68-120">HTTPS 프로토콜을 사용할 수 있는 앱을 호스트하는 방법에 대한 자세한 내용은 이 문서의 [추가 리소스](#additional-resources) 섹션에 있는 보안 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2c68-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="e2c68-121">ASP.NET Core 앱을 호스트하기 위한 추가 지침은 <xref:host-and-deploy/iis/index> 문서에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e2c68-122">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="e2c68-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e2c68-123">IIS 서버에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="e2c68-124">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e2c68-125">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-125">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="e2c68-126">시스템이 인터넷에 연결되지 않은 경우 [Microsoft Visual C++ 2015 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=53840)를 설치한 후에 .NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-126">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

<span data-ttu-id="e2c68-127">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-127">Download the installer using the following link:</span></span>

[<span data-ttu-id="e2c68-128">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="e2c68-128">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="e2c68-129">IIS 서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-129">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="e2c68-130">서버를 다시 시작하거나 명령 셸에서 **net stop was /y**, **net start w3svc**를 차례로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-130">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e2c68-131">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="e2c68-131">Create the IIS site</span></span>

1. <span data-ttu-id="e2c68-132">IIS 서버에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-132">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e2c68-133">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-133">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="e2c68-134">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-134">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e2c68-135">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-135">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e2c68-136">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-136">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e2c68-137">**사이트 이름**을 입력하고 **실제 경로**를 만든 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-137">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="e2c68-138">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-138">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="e2c68-139">ASP.NET Core Razor Pages 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e2c68-139">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="e2c68-140"><xref:getting-started> 자습서에 따라 Razor Pages 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-140">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e2c68-141">앱 게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="e2c68-141">Publish and deploy the app</span></span>

<span data-ttu-id="e2c68-142">‘앱 게시’는 서버에서 호스트할 수 있는 컴파일된 앱을 생성하는 것을 의미합니다. </span><span class="sxs-lookup"><span data-stu-id="e2c68-142">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="e2c68-143">‘앱 배포’는 게시된 앱을 호스팅 시스템으로 이동하는 것을 의미합니다. </span><span class="sxs-lookup"><span data-stu-id="e2c68-143">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="e2c68-144">게시 단계는 [.NET Core SDK](/dotnet/core/sdk)에서 처리되지만, 배포 단계는 다양한 방법으로 처리될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-144">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="e2c68-145">이 자습서에서는 다음과 같은 ‘폴더’ 배포 방법을 채택합니다. </span><span class="sxs-lookup"><span data-stu-id="e2c68-145">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="e2c68-146">앱은 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-146">The app is published to a folder.</span></span>
* <span data-ttu-id="e2c68-147">폴더의 콘텐츠는 IIS 사이트의 폴더(IIS 관리자에 있는 사이트의 **실제 경로**)로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-147">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c68-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c68-148">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e2c68-149">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-149">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="e2c68-150">**게시 대상 선택** 대화 상자에서 **폴더** 게시 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-150">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="e2c68-151">**폴더 또는 파일 공유** 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-151">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="e2c68-152">개발 머신에서 네트워크 공유로 사용할 수 있는 IIS 사이트의 폴더를 만든 경우 공유 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-152">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e2c68-153">공유에 게시하려면 현재 사용자에게 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-153">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e2c68-154">IIS 서버의 IIS 사이트 폴더에 직접 배포할 수 없는 경우 이동식 미디어의 폴더에 게시하고 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 게시된 앱을 실제로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-154">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e2c68-155">*bin/Release/{TARGET FRAMEWORK}/publish* 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-155">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e2c68-156">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e2c68-156">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="e2c68-157">명령 셸에서 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성에 있는 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-157">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="e2c68-158">*bin/Release/{TARGET FRAMEWORK}/publish* 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-158">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c68-159">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e2c68-159">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e2c68-160">**솔루션**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** > **폴더에 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-160">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="e2c68-161">**폴더 선택** 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-161">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="e2c68-162">개발 머신에서 네트워크 공유로 사용할 수 있는 IIS 사이트의 폴더를 만든 경우 공유 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-162">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e2c68-163">공유에 게시하려면 현재 사용자에게 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-163">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e2c68-164">IIS 서버의 IIS 사이트 폴더에 직접 배포할 수 없는 경우 이동식 미디어의 폴더에 게시하고 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 게시된 앱을 실제로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-164">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e2c68-165">*bin/Release/{TARGET FRAMEWORK}/publish* 폴더의 콘텐츠를 서버의 IIS 사이트 폴더(IIS 관리자에서 사이트의 **실제 경로**)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-165">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="e2c68-166">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="e2c68-166">Browse the website</span></span>

<span data-ttu-id="e2c68-167">앱은 첫 번째 요청을 받은 후 브라우저에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-167">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="e2c68-168">IIS 관리자에서 사이트에 대해 설정한 엔드포인트 바인딩에서 앱에 대한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-168">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e2c68-169">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e2c68-169">Next steps</span></span>

<span data-ttu-id="e2c68-170">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-170">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e2c68-171">Windows Server에 .NET Core 호스팅 번들을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-171">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e2c68-172">IIS 관리자에서 IIS 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-172">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e2c68-173">ASP.NET Core 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e2c68-173">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="e2c68-174">IIS에서 ASP.NET Core 앱을 호스트하는 방법에 대한 자세한 내용은 IIS 개요 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2c68-174">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="e2c68-175">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2c68-175">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="e2c68-176">ASP.NET Core 설명서 세트의 문서</span><span class="sxs-lookup"><span data-stu-id="e2c68-176">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="e2c68-177">ASP.NET Core 앱 배포와 관련된 문서</span><span class="sxs-lookup"><span data-stu-id="e2c68-177">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="e2c68-178">Mac용 Visual Studio를 사용하여 폴더에 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="e2c68-178">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="e2c68-179">IIS HTTPS 구성에 대한 문서</span><span class="sxs-lookup"><span data-stu-id="e2c68-179">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="e2c68-180">IIS 관리자에서 SSL 구성</span><span class="sxs-lookup"><span data-stu-id="e2c68-180">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="e2c68-181">IIS에서 SSL을 설정하는 방법</span><span class="sxs-lookup"><span data-stu-id="e2c68-181">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="e2c68-182">IIS 및 Windows Server에 대한 문서</span><span class="sxs-lookup"><span data-stu-id="e2c68-182">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="e2c68-183">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="e2c68-183">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e2c68-184">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e2c68-184">Windows Server technical content library</span></span>](/windows-server/windows-server)