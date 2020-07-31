---
title: ASP.NET Core에서 Windows 인증 구성
author: scottaddie
description: IIS 및 HTTP.sys에 대 한 ASP.NET Core에서 Windows 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330688"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="10e2f-103">ASP.NET Core에서 Windows 인증 구성</span><span class="sxs-lookup"><span data-stu-id="10e2f-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="10e2f-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="10e2f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10e2f-105">[IIS](xref:host-and-deploy/iis/index), [kestrel](xref:fundamentals/servers/kestrel)또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용 하 여 호스트 되는 ASP.NET Core 앱에 대해 Windows 인증 (Negotiate, Kerberos 또는 NTLM 인증이 라고도 함)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10e2f-106">[IIS](xref:host-and-deploy/iis/index) 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용 하 여 호스트 되는 ASP.NET Core 앱에 대해 Windows 인증 (NEGOTIATE, Kerberos 또는 NTLM 인증이 라고도 함)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="10e2f-107">Windows 인증은 운영 체제를 사용 하 여 ASP.NET Core 앱의 사용자를 인증 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="10e2f-108">사용자를 식별 하기 위해 Active Directory 도메인 id 또는 Windows 계정을 사용 하 여 회사 네트워크에서 서버를 실행 하는 경우 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="10e2f-109">Windows 인증은 사용자, 클라이언트 앱 및 웹 서버가 동일한 Windows 도메인에 속하는 인트라넷 환경에 가장 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-110">HTTP/2에서는 Windows 인증이 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="10e2f-111">인증 문제는 HTTP/2 응답에서 전송 될 수 있지만 인증 하기 전에 클라이언트에서 HTTP/1.1로 다운 그레이드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="10e2f-112">프록시 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="10e2f-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="10e2f-113">Windows 인증은 주로 인트라넷에서 사용 되는 상태 저장 시나리오로, 일반적으로 프록시 또는 부하 분산 장치는 클라이언트와 서버 간의 트래픽을 처리 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="10e2f-114">프록시 또는 부하 분산 장치를 사용 하는 경우 Windows 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="10e2f-115">인증을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-115">Handles the authentication.</span></span>
* <span data-ttu-id="10e2f-116">인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="10e2f-117">프록시 및 부하 분산 장치를 사용 하는 환경에서 Windows 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="10e2f-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="10e2f-118">IIS/IIS Express</span></span>

<span data-ttu-id="10e2f-119"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 다음 위치에서 (네임 스페이스)를 호출 하 여 인증 서비스를 추가 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="10e2f-120">시작 설정 (디버거)</span><span class="sxs-lookup"><span data-stu-id="10e2f-120">Launch settings (debugger)</span></span>

<span data-ttu-id="10e2f-121">시작 설정 구성은 IIS Express 파일의 *속성/launchSettings.js* 에만 영향을 주며 WINDOWS 인증용 IIS는 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="10e2f-122">서버 구성은 [IIS](#iis) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="10e2f-123">Visual Studio 또는 .NET Core CLI를 통해 사용할 수 있는 **웹 응용 프로그램** 템플릿은 Windows 인증을 지원 하도록 구성 될 수 있습니다. 그러면 파일에서 자동으로 *속성/launchSettings.js* 를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10e2f-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10e2f-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10e2f-125">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="10e2f-125">**New project**</span></span>

1. <span data-ttu-id="10e2f-126">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-126">Create a new project.</span></span>
1. <span data-ttu-id="10e2f-127">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="10e2f-128">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-128">Select **Next**.</span></span>
1. <span data-ttu-id="10e2f-129">**프로젝트 이름** 필드에 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="10e2f-130">**위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="10e2f-131">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-131">Select **Create**.</span></span>
1. <span data-ttu-id="10e2f-132">**인증**에서 **변경** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="10e2f-133">**인증 변경** 창에서 **Windows 인증**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="10e2f-134">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-134">Select **OK**.</span></span>
1. <span data-ttu-id="10e2f-135">**웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="10e2f-136">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-136">Select **Create**.</span></span>

<span data-ttu-id="10e2f-137">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-137">Run the app.</span></span> <span data-ttu-id="10e2f-138">사용자 이름은 렌더링 된 앱의 사용자 인터페이스에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="10e2f-139">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="10e2f-139">**Existing project**</span></span>

<span data-ttu-id="10e2f-140">프로젝트의 속성은 Windows 인증을 사용 하도록 설정 하 고 익명 인증을 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="10e2f-141">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="10e2f-142">**디버그** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="10e2f-143">**익명 인증 사용**에 대 한 확인란의 선택을 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="10e2f-144">**Windows 인증 사용**확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="10e2f-145">속성 페이지를 저장 하 고 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-145">Save and close the property page.</span></span>

<span data-ttu-id="10e2f-146">또는 파일의launchSettings.js노드에서 속성을 구성할 수 있습니다 `iisSettings` . *launchSettings.json*</span><span class="sxs-lookup"><span data-stu-id="10e2f-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="10e2f-147">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="10e2f-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="10e2f-148">**새 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="10e2f-148">**New project**</span></span>

<span data-ttu-id="10e2f-149">인수 (ASP.NET Core 웹 앱)를 사용 하 여 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 실행 `webapp` 하 고 `--auth Windows` 다음을 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="10e2f-150">**기존 프로젝트**</span><span class="sxs-lookup"><span data-stu-id="10e2f-150">**Existing project**</span></span>

<span data-ttu-id="10e2f-151">파일의 `iisSettings` *launchSettings.js* 노드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="10e2f-152">기존 프로젝트를 수정할 때는 프로젝트 파일에 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) **또는** [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지에 대 한 패키지 참조가 포함 되어 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="10e2f-153">IIS</span><span class="sxs-lookup"><span data-stu-id="10e2f-153">IIS</span></span>

<span data-ttu-id="10e2f-154">IIS는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 을 사용 하 여 ASP.NET Core 앱을 호스팅합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="10e2f-155">Windows 인증은 *web.config* 파일을 통해 IIS에 대해 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="10e2f-156">다음 섹션에서는 수행 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-156">The following sections show how to:</span></span>

* <span data-ttu-id="10e2f-157">앱이 배포 될 때 서버에서 Windows 인증을 활성화 하는 로컬 *web.config* 파일을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="10e2f-158">IIS 관리자를 사용 하 여 서버에 이미 배포 된 ASP.NET Core 앱의 *web.config* 파일을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="10e2f-159">아직 수행 하지 않은 경우 IIS에서 ASP.NET Core 앱을 호스트할 수 있도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="10e2f-160">자세한 내용은 <xref:host-and-deploy/iis/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="10e2f-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="10e2f-161">Windows 인증에 대해 IIS 역할 서비스를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="10e2f-162">자세한 내용은 [IIS 역할 서비스에서 Windows 인증 사용 (2 단계 참조)](xref:host-and-deploy/iis/index#iis-configuration)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="10e2f-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="10e2f-163">[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 는 기본적으로 요청을 자동으로 인증 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="10e2f-164">자세한 내용은 [iis를 사용 하 여 Windows에서 호스트 ASP.NET Core: iis 옵션 (자동 인증)](xref:host-and-deploy/iis/index#iis-options)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="10e2f-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="10e2f-165">ASP.NET Core 모듈은 기본적으로 Windows 인증 토큰을 앱에 전달 하도록 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="10e2f-166">자세한 내용은 [ASP.NET Core 모듈 구성 참조: aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="10e2f-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="10e2f-167">다음 방법 중 **하나** 를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="10e2f-168">**프로젝트를 게시 하 고 배포 하기 전에** 프로젝트 루트에 다음 *web.config* 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="10e2f-169">프로젝트 파일에서 속성을로 설정 하지 않고 .NET Core SDK에서 프로젝트를 게시 하면 `<IsTransformWebConfigDisabled>` `true` 게시 된 *web.config* 파일에 섹션이 포함 됩니다 `<location><system.webServer><security><authentication>` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="10e2f-170">에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="10e2f-171">**프로젝트를 게시 하 고 배포한 후** 에는 IIS 관리자를 사용 하 여 서버 쪽 구성을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="10e2f-172">IIS 관리자의 **연결** 세로 막대에 있는 **사이트** 노드 아래에서 iis 사이트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="10e2f-173">**IIS** 영역에서 **인증** 을 두 번 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="10e2f-174">**익명 인증**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="10e2f-175">**작업** 사이드바에서 **사용 안 함** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="10e2f-176">**Windows 인증**을 선택하고</span><span class="sxs-lookup"><span data-stu-id="10e2f-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="10e2f-177">**작업** 사이드바에서 **사용** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="10e2f-178">이러한 작업을 수행 하는 경우 IIS 관리자는 앱의 *web.config* 파일을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="10e2f-179">`<system.webServer><security><authentication>`및에 대 한 업데이트 된 설정이 포함 된 노드가 추가 됩니다 `anonymousAuthentication` `windowsAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="10e2f-180">`<system.webServer>`IIS 관리자에서 *web.config* 파일에 추가 하는 섹션은 `<location>` 앱이 게시 될 때 .NET Core SDK에 의해 추가 된 앱 섹션을 벗어납니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="10e2f-181">섹션이 노드 외부에 추가 되기 때문에 `<location>` 모든 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 에서 현재 앱에 대 한 설정이 상속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="10e2f-182">상속을 방지 하려면 `<security>` .NET Core SDK 제공 된 섹션 내에서 추가 된 섹션을 이동 합니다 `<location><system.webServer>` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="10e2f-183">Iis 관리자를 사용 하 여 IIS 구성을 추가 하면 서버에 있는 앱의 *web.config* 파일에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="10e2f-184">서버의 *web.config* 복사본이 프로젝트의 *web.config* 파일로 대체 되 면 앱의 후속 배포에서 서버의 설정을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="10e2f-185">다음 방법 중 **하나** 를 사용 하 여 설정을 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="10e2f-186">배포 시 파일을 덮어쓴 후에 IIS 관리자를 사용 하 여 *web.config* 파일의 설정을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="10e2f-187">설정을 사용 하 여 로컬에 *web.config 파일* 을 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="10e2f-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="10e2f-188">Kestrel</span></span>

<span data-ttu-id="10e2f-189">Windows, Linux 및 macOS에서 Negotiate 및 Kerberos를 사용 하 여 Windows 인증을 지원 하기 위해 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 패키지를 [kestrel](xref:fundamentals/servers/kestrel) 과 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="10e2f-190">자격 증명은 연결에 대 한 요청에서 지속 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="10e2f-191">*프록시가 Kestrel을 사용 하 여 1:1 연결 선호도 (영구 연결)를 유지 관리 하지 않는 한 Negotiate authentication은 프록시와 함께 사용 하면 안 됩니다.*</span><span class="sxs-lookup"><span data-stu-id="10e2f-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-192">Negotiate 처리기는 기본 서버가 Windows 인증을 기본적으로 지원 하는지 여부를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="10e2f-193">서버에서 Windows 인증을 지원 하지만 사용할 수 없는 경우 서버 구현을 사용 하도록 요청 하는 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="10e2f-194">서버에서 Windows 인증을 사용 하도록 설정 하면 Negotiate 핸들러가 투명 하 게이를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="10e2f-195">에서 및을 호출 하 여 인증 서비스를 추가 합니다 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="10e2f-196">에서를 호출 하 여 인증 미들웨어를 추가 합니다 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="10e2f-197">미들웨어에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="10e2f-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="10e2f-198">익명 요청은 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="10e2f-199">[ASP.NET Core 권한 부여](xref:security/authorization/introduction) 를 사용 하 여 인증에 대 한 익명 요청을 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="10e2f-200">Windows 환경 구성</span><span class="sxs-lookup"><span data-stu-id="10e2f-200">Windows environment configuration</span></span>

<span data-ttu-id="10e2f-201">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 구성 요소는 사용자 모드 인증을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="10e2f-202">Spn (서비스 사용자 이름)은 컴퓨터 계정이 아니라 서비스를 실행 하는 사용자 계정에 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="10e2f-203">`setspn -S HTTP/myservername.mydomain.com myuser`관리 명령 셸에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="10e2f-204">Linux 및 macOS 환경 구성</span><span class="sxs-lookup"><span data-stu-id="10e2f-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="10e2f-205">Linux 또는 macOS 컴퓨터를 Windows 도메인에 가입 하는 방법에 대 한 지침은 [windows 인증을 사용 하 여 SQL Server에 연결 Azure Data Studio-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="10e2f-206">지침은 도메인에서 Linux 컴퓨터에 대 한 컴퓨터 계정을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="10e2f-207">해당 컴퓨터 계정에 Spn을 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-208">[Windows 인증을 사용 하 여 SQL Server에 Azure Data Studio 연결-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서의 지침에 따라 `python-software-properties` 필요한 경우을로 바꿉니다 `python3-software-properties` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="10e2f-209">Linux 또는 macOS 컴퓨터가 도메인에 가입 된 후에는 [keytab 파일](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) 을 spn과 함께 제공 하기 위해 추가 단계가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="10e2f-210">도메인 컨트롤러에서 컴퓨터 계정에 새 웹 서비스 Spn을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="10e2f-211">[Ktpass](/windows-server/administration/windows-commands/ktpass) 를 사용 하 여 keytab 파일을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="10e2f-212">일부 필드는 표시 된 대로 대문자로 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="10e2f-213">Linux 또는 macOS 컴퓨터에 keytab 파일을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="10e2f-214">환경 변수를 통해 keytab 파일을 선택 합니다.`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="10e2f-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="10e2f-215">`klist`을 호출 하 여 현재 사용할 수 있는 spn을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-216">Keytab 파일은 도메인 액세스 자격 증명을 포함 하므로 적절 하 게 보호 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="10e2f-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10e2f-217">HTTP.sys</span></span>

<span data-ttu-id="10e2f-218">[HTTP.sys](xref:fundamentals/servers/httpsys) 은 NEGOTIATE, NTLM 또는 기본 인증을 사용 하는 커널 모드 Windows 인증을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="10e2f-219"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 다음 위치에서 (네임 스페이스)를 호출 하 여 인증 서비스를 추가 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="10e2f-220">Windows 인증 (*Program.cs*)과 함께 HTTP.sys를 사용 하도록 앱의 웹 호스트를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="10e2f-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>는 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="10e2f-222">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10e2f-223">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10e2f-224">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10e2f-225">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-226">HTTP.sys은 Nano Server 버전 1709 이상에서 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="10e2f-227">Nano Server에서 Windows 인증 및 HTTP.sys을 사용 하려면 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="10e2f-228">Server Core에 대 한 자세한 내용은 [Windows server에서 Server core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="10e2f-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="10e2f-229">사용자 권한 부여</span><span class="sxs-lookup"><span data-stu-id="10e2f-229">Authorize users</span></span>

<span data-ttu-id="10e2f-230">익명 액세스의 구성 상태는 `[Authorize]` 앱에서 및 특성을 사용 하는 방법을 결정 합니다 `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="10e2f-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="10e2f-231">다음 두 섹션에서는 익명 액세스의 허용 되지 않는 구성 상태 및 허용 되는 구성 상태를 처리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="10e2f-232">익명 액세스 허용 안 함</span><span class="sxs-lookup"><span data-stu-id="10e2f-232">Disallow anonymous access</span></span>

<span data-ttu-id="10e2f-233">Windows 인증을 사용 하도록 설정 하 고 익명 액세스를 사용 하지 않도록 설정 하면 및 특성은 아무런 영향을 주지 `[Authorize]` `[AllowAnonymous]` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="10e2f-234">IIS 사이트에서 익명 액세스를 허용 하지 않도록 구성 된 경우 요청은 앱에 도달 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="10e2f-235">따라서 `[AllowAnonymous]` 특성을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="10e2f-236">익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="10e2f-236">Allow anonymous access</span></span>

<span data-ttu-id="10e2f-237">Windows 인증 및 익명 액세스를 모두 사용 하는 경우 `[Authorize]` 및 특성을 사용 `[AllowAnonymous]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="10e2f-238">`[Authorize]`특성을 사용 하면 인증이 필요한 앱의 끝점을 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="10e2f-239">`[AllowAnonymous]`특성은 `[Authorize]` 익명 액세스를 허용 하는 앱의 특성을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="10e2f-240">특성 사용에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="10e2f-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="10e2f-241">기본적으로 페이지에 대 한 액세스 권한이 없는 사용자에 게는 빈 HTTP 403 응답이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="10e2f-242">사용자에 게 더 나은 "액세스 거부" 환경을 제공 하도록 [Statuscodepages 페이지 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="10e2f-243">가장</span><span class="sxs-lookup"><span data-stu-id="10e2f-243">Impersonation</span></span>

<span data-ttu-id="10e2f-244">ASP.NET Core 가장을 구현 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="10e2f-245">앱은 앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱 id로 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="10e2f-246">앱이 사용자를 대신 하 여 작업을 수행 해야 하는 경우의 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 [가장 된 WindowsIdentity](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 를 사용 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="10e2f-247">이 컨텍스트에서 단일 작업을 실행 한 다음 컨텍스트를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="10e2f-248">`RunImpersonated`는 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="10e2f-249">예를 들어 전체 요청 또는 미들웨어 체인 래핑은 지원 되지 않거나 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10e2f-250">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지는 Windows, Linux 및 macos에서 인증을 사용 하도록 설정 하는 반면 가장은 windows 에서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="10e2f-251">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="10e2f-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10e2f-252">IIS를 사용 하 여 호스트할 때 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 는 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="10e2f-253">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="10e2f-254">클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은을 참조 하십시오 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="10e2f-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10e2f-255">IIS in-process 모드로 호스트 하는 경우 사용자를 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 초기화 하기 위해가 내부적으로 호출 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="10e2f-256">따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="10e2f-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="10e2f-257">In-process를 호스팅할 때 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은을 참조 하십시오 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="10e2f-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="10e2f-258">추가 자료</span><span class="sxs-lookup"><span data-stu-id="10e2f-258">Additional resources</span></span>

* [<span data-ttu-id="10e2f-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="10e2f-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
