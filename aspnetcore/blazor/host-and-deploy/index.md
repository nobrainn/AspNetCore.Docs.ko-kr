---
title: ASP.NET Core 호스트 및 배포 Blazor
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103152"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="a6a24-103">ASP.NET Core 호스트 및 배포 Blazor</span><span class="sxs-lookup"><span data-stu-id="a6a24-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="a6a24-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a6a24-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="a6a24-105">앱 게시</span><span class="sxs-lookup"><span data-stu-id="a6a24-105">Publish the app</span></span>

<span data-ttu-id="a6a24-106">앱은 릴리스 구성으로 배포하기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6a24-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6a24-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a6a24-108">탐색 모음에서 **빌드** >  **{APPLICATION} 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="a6a24-109">*publish target*을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-109">Select the *publish target*.</span></span> <span data-ttu-id="a6a24-110">로컬로 게시하려면 **폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="a6a24-111">**폴더 선택** 필드에서 기본 위치를 그대로 사용하거나 다른 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="a6a24-112">**게시** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6a24-113">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6a24-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a6a24-114">**빌드** > **폴더에 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="a6a24-115">게시된 자산을 받을 폴더를 확인하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a6a24-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a6a24-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6a24-117">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="a6a24-118">앱을 게시하면 배포할 자산을 만들기 전에 프로젝트 종속성의 [복원](/dotnet/core/tools/dotnet-restore)과 프로젝트의 [빌드](/dotnet/core/tools/dotnet-build)가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="a6a24-119">빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="a6a24-120">게시 위치:</span><span class="sxs-lookup"><span data-stu-id="a6a24-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="a6a24-121"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a6a24-121"> WebAssembly</span></span>
  * <span data-ttu-id="a6a24-122">독립 실행형: 해당 앱은 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="a6a24-123">앱을 정적 사이트로 배포하려면 *wwwroot* 폴더의 내용을 정적 사이트 호스트에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="a6a24-124">호스트형: 클라이언트 Blazor WebAssembly 앱이 서버 앱의 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="a6a24-125">호스트에 *게시* 폴더의 콘텐츠를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="a6a24-126"> 서버: 해당 앱이 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-126"> Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="a6a24-127">호스트에 *게시* 폴더의 콘텐츠를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="a6a24-128">이 폴더의 자산은 웹 서버에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="a6a24-129">배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="a6a24-130">앱 기본 경로</span><span class="sxs-lookup"><span data-stu-id="a6a24-130">App base path</span></span>

<span data-ttu-id="a6a24-131">*앱 기본 경로*는 앱의 루트 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="a6a24-132">다음 ASP.NET Core 앱 및 Blazor 하위 앱을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="a6a24-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="a6a24-133">ASP.NET Core 앱은 `MyApp`으로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="a6a24-134">앱은 실제로 *d:/MyApp*에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="a6a24-135">요청은 `https://www.contoso.com/{MYAPP RESOURCE}`에서 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="a6a24-136">`CoolApp`이라는 이름의 Blazor 앱은 `MyApp`의 하위 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="a6a24-137">하위 앱은 물리적으로 *d:/MyApp/CoolApp*에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="a6a24-138">요청은 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`에서 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="a6a24-139">`CoolApp`에 대한 추가 구성을 지정하지 않으면 이 시나리오의 하위 앱은 서버에 상주하는 위치에 대해 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="a6a24-140">예를 들어 앱은 상대 URL 경로 `/CoolApp/`에 상주한다는 사실을 모르는 상태에서는 해당 리소스의 올바른 상대 URL을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="a6a24-141">`https://www.contoso.com/CoolApp/`이라는 Blazor 앱의 기본 경로에 대한 구성을 제공하기 위해 `<base>` 태그의 `href` 특성은 *Pages/_Host.cshtml* 파일(Blazor 서버) 또는 *wwwroot/index.html* 파일(Blazor WebAssembly)의 상대 루트 경로로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="a6a24-142"> 서버 앱은 `Startup.Configure`의 앱의 요청 파이프라인에서 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>를 호출하여 서버 쪽 기본 경로를 추가로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="a6a24-143">상대 URL 경로를 제공하면 루트 디렉터리에 없는 구성 요소는 앱의 루트 경로를 기준으로 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="a6a24-144">디렉터리 구조의 다른 수준에 있는 구성 요소는 앱 전체의 위치에서 다른 리소스에 대한 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="a6a24-145">또한 링크의 `href` 대상이 앱 기본 경로 URI 공간 내에 있는 경우 선택한 하이퍼링크를 가로채는 데 앱 기본 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="a6a24-146">Blazor 라우터는 내부 탐색을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="a6a24-147">많은 호스팅 시나리오에서 앱에 대한 상대 URL 경로는 앱의 루트입니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="a6a24-148">이러한 경우 앱의 상대 URL 기본 경로는 Blazor 앱에 대한 기본 구성인 슬래시(`<base href="/" />`)입니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="a6a24-149">GitHub 페이지 및 IIS 하위 앱 같은 다른 호스팅 시나리오에서는 앱 기본 경로를 앱에 대한 서버의 상대 URL 경로로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="a6a24-150">앱의 기본 경로를 설정하려면 *Pages/_Host.cshtml* 파일(Blazor 서버) 또는 *wwwroot/index.html* 파일(Blazor WebAssembly)의 `<head>` 태그 요소 내에 `<base>` 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="a6a24-151">`href` 특성 값을 `/{RELATIVE URL PATH}/`(뒤에 슬래시가 필요함)로 설정합니다. 여기서 `{RELATIVE URL PATH}`는 앱의 전체 상대 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="a6a24-152">루트가 아닌 상대 URL 경로를 가진 Blazor WebAssembly 앱(예: `<base href="/CoolApp/">`)의 경우, 앱은 *로컬로 실행하면* 해당 리소스를 찾지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="a6a24-153">로컬 개발 및 시험 중에 이 문제를 해결하려면 런타임에 `<base>` 태그의 `href` 값과 일치하는 *기본 경로* 인수를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="a6a24-154">후행 슬래시를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-154">Don't include a trailing slash.</span></span> <span data-ttu-id="a6a24-155">앱을 로컬로 실행하는 경우 경로 기본 인수를 전달하려면 `--pathbase` 옵션을 통해 앱의 디렉터리에서 `dotnet run` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="a6a24-156">`/CoolApp/`의 상대 URL 경로를 사용하는 Blazor WebAssembly 앱의 경우(`<base href="/CoolApp/">`) 명령은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="a6a24-157">Blazor WebAssembly 앱은 `http://localhost:port/CoolApp`에서 로컬로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a6a24-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="a6a24-158">배포</span><span class="sxs-lookup"><span data-stu-id="a6a24-158">Deployment</span></span>

<span data-ttu-id="a6a24-159">배포 지침은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6a24-159">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
