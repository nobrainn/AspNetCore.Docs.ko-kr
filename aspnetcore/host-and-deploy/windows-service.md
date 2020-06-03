---
<span data-ttu-id="d43f0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d43f0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d43f0-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d43f0-102">'Blazor'</span></span>
- <span data-ttu-id="d43f0-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d43f0-103">'Identity'</span></span>
- <span data-ttu-id="d43f0-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d43f0-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d43f0-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d43f0-105">'Razor'</span></span>
- <span data-ttu-id="d43f0-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="d43f0-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="d43f0-107">Windows 서비스에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="d43f0-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d43f0-108">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d43f0-109">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d43f0-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d43f0-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d43f0-111">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d43f0-111">Prerequisites</span></span>

* [<span data-ttu-id="d43f0-112">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d43f0-113">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="d43f0-114">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="d43f0-114">Worker Service template</span></span>

<span data-ttu-id="d43f0-115">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d43f0-116">템플릿을 Windows Service 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="d43f0-117">.NET Core 템플릿에서 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="d43f0-118">[앱 구성](#app-configuration) 섹션의 지침에 따라 Windows Service로 실행할 수 있도록 Worker Service 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="d43f0-119">앱 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-119">App configuration</span></span>

<span data-ttu-id="d43f0-120">앱에는 [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="d43f0-121">`IHostBuilder.UseWindowsService`는 호스트를 빌드할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="d43f0-122">앱이 Windows 서비스로 실행되는 경우 이 메서드는 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="d43f0-123">호스트 수명을 `WindowsServiceLifetime`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="d43f0-124">[콘텐츠 루트](xref:fundamentals/index#content-root)를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="d43f0-125">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="d43f0-126">이벤트 로그에 대한 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="d43f0-127">애플리케이션 이름이 기본 소스 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="d43f0-128">호스트를 빌드하기 위해 `CreateDefaultBuilder`를 호출하는 ASP.NET Core 템플릿 기반 앱에 대한 기본 로그 수준은 *경고* 이상입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="d43f0-129">*appsettings.json*/*appsettings.{Environment}.json*의 `Logging:EventLog:LogLevel:Default` 키 또는 다른 구성 공급자로 기본 로그 수준을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="d43f0-130">관리자만 새 이벤트 소스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="d43f0-131">애플리케이션 이름을 사용하여 이벤트 소스를 만들 수 없는 경우 *Application* 소스에 경고가 기록되고 이벤트 로그가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="d43f0-132">*Program.cs*의 `CreateHostBuilder`에서:</span><span class="sxs-lookup"><span data-stu-id="d43f0-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="d43f0-133">다음 샘플 앱은 이 항목과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="d43f0-134">백그라운드 작업자 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하는 [작업자 서비스 템플릿](#worker-service-template)을 기반으로 하는 비 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="d43f0-135">웹앱 서비스 샘플: 백그라운드 작업에 [호스팅된 서비스](xref:fundamentals/host/hosted-services)를 사용하여 Windows 서비스로 실행되는 Razor Pages 웹앱 샘플입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="d43f0-136">MVC 지침은 <xref:mvc/overview> 및 <xref:migration/22-to-30>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="d43f0-137">배포 유형</span><span class="sxs-lookup"><span data-stu-id="d43f0-137">Deployment type</span></span>

<span data-ttu-id="d43f0-138">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d43f0-139">SDK</span><span class="sxs-lookup"><span data-stu-id="d43f0-139">SDK</span></span>

<span data-ttu-id="d43f0-140">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d43f0-141">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d43f0-142">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d43f0-143">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d43f0-144">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일*이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d43f0-145">[Web SDK](#sdk)를 사용할 경우 ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d43f0-146">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d43f0-147">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d43f0-148">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d43f0-149">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d43f0-150">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d43f0-151">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="d43f0-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d43f0-152">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d43f0-153">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d43f0-154">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="d43f0-155">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="d43f0-155">Service user account</span></span>

<span data-ttu-id="d43f0-156">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d43f0-157">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-158">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d43f0-159">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d43f0-160">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d43f0-161">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d43f0-162">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d43f0-163">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d43f0-164">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="d43f0-164">Log on as a service rights</span></span>

<span data-ttu-id="d43f0-165">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="d43f0-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d43f0-166">*secpol.msc*를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d43f0-167">**로컬 정책** 노드를 확장하고 **사용자 권한 할당**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d43f0-168">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d43f0-169">**사용자 또는 그룹 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d43f0-170">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d43f0-171">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d43f0-172">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-172">Select **Advanced**.</span></span> <span data-ttu-id="d43f0-173">**지금 찾기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-173">Select **Find Now**.</span></span> <span data-ttu-id="d43f0-174">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-174">Select the user account from the list.</span></span> <span data-ttu-id="d43f0-175">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-175">Select **OK**.</span></span> <span data-ttu-id="d43f0-176">다시 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d43f0-177">**확인** 또는 **적용**을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d43f0-178">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="d43f0-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d43f0-179">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="d43f0-179">Create a service</span></span>

<span data-ttu-id="d43f0-180">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d43f0-181">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d43f0-182">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d43f0-183">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d43f0-184">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d43f0-185">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d43f0-186">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d43f0-187">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d43f0-188">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d43f0-189">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d43f0-190">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d43f0-191">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="d43f0-191">Start a service</span></span>

<span data-ttu-id="d43f0-192">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-193">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d43f0-194">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="d43f0-194">Determine a service's status</span></span>

<span data-ttu-id="d43f0-195">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-196">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d43f0-197">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="d43f0-197">Stop a service</span></span>

<span data-ttu-id="d43f0-198">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d43f0-199">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="d43f0-199">Remove a service</span></span>

<span data-ttu-id="d43f0-200">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d43f0-201">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="d43f0-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d43f0-202">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d43f0-203">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d43f0-204">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-204">Configure endpoints</span></span>

<span data-ttu-id="d43f0-205">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d43f0-206">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d43f0-207">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d43f0-208">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d43f0-209">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d43f0-210">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d43f0-211">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d43f0-211">Current directory and content root</span></span>

<span data-ttu-id="d43f0-212">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d43f0-213">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d43f0-214">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="d43f0-215">ContentRootPath 또는 ContentRootFileProvider 사용</span><span class="sxs-lookup"><span data-stu-id="d43f0-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="d43f0-216">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 또는 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>를 사용하여 앱의 리소스를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="d43f0-217">앱이 서비스로 실행되면 <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*>가 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath>를 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="d43f0-218">앱의 기본 설정 파일 *appsettings.json* 및 *appsettings.{Environment}.json*은 호스트 생성 도중 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host)를 호출하여 앱의 콘텐츠 루트에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="d43f0-219"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에서 개발자 코드로 로드되는 다른 설정 파일의 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="d43f0-220">다음 예제에서 *custom_settings.json* 파일은 앱의 콘텐츠 루트에 있으며 기본 경로를 명시적으로 설정하지 않고 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="d43f0-221">Windows Service 앱은 *C:\\WINDOWS\\system32* 폴더를 현재 디렉터리로 반환하기 때문에 <xref:System.IO.Directory.GetCurrentDirectory*>를 사용하여 리소스 경로를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d43f0-222">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="d43f0-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d43f0-223">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d43f0-224">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d43f0-224">Troubleshoot</span></span>

<span data-ttu-id="d43f0-225">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d43f0-226">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="d43f0-226">Common errors</span></span>

* <span data-ttu-id="d43f0-227">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d43f0-228">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d43f0-229">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d43f0-230">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d43f0-231">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d43f0-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d43f0-233">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d43f0-234">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d43f0-235">Windows Service의 기본 경로는 *c:\\Windows\\System32*입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d43f0-236">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d43f0-237">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d43f0-238">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d43f0-239">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d43f0-240">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d43f0-240">System and Application Event Logs</span></span>

<span data-ttu-id="d43f0-241">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="d43f0-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d43f0-242">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d43f0-243">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d43f0-244">**시스템**을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d43f0-245">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d43f0-246">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d43f0-247">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-247">Run the app at a command prompt</span></span>

<span data-ttu-id="d43f0-248">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d43f0-249">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d43f0-250">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d43f0-251">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="d43f0-251">Clear package caches</span></span>

<span data-ttu-id="d43f0-252">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d43f0-253">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d43f0-254">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d43f0-255">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d43f0-256">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d43f0-257">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d43f0-258">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d43f0-259">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d43f0-260">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d43f0-261">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="d43f0-261">Slow or hanging app</span></span>

<span data-ttu-id="d43f0-262">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d43f0-263">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="d43f0-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="d43f0-264">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d43f0-265">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d43f0-266">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d43f0-267">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d43f0-268">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="d43f0-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d43f0-269">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d43f0-270">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d43f0-271">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d43f0-272">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d43f0-273">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d43f0-274">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="d43f0-274">Analyze the dump</span></span>

<span data-ttu-id="d43f0-275">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d43f0-276">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d43f0-277">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d43f0-277">Additional resources</span></span>

* <span data-ttu-id="d43f0-278">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="d43f0-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d43f0-279">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d43f0-280">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d43f0-281">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d43f0-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d43f0-282">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d43f0-282">Prerequisites</span></span>

* [<span data-ttu-id="d43f0-283">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d43f0-284">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d43f0-285">앱 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-285">App configuration</span></span>

<span data-ttu-id="d43f0-286">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d43f0-287">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d43f0-288">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d43f0-289">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d43f0-290">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d43f0-291"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d43f0-292"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d43f0-293">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d43f0-294">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d43f0-295"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d43f0-296">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d43f0-297">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d43f0-298">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d43f0-299">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d43f0-300">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d43f0-301">배포 유형</span><span class="sxs-lookup"><span data-stu-id="d43f0-301">Deployment type</span></span>

<span data-ttu-id="d43f0-302">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d43f0-303">SDK</span><span class="sxs-lookup"><span data-stu-id="d43f0-303">SDK</span></span>

<span data-ttu-id="d43f0-304">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d43f0-305">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d43f0-306">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d43f0-307">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d43f0-308">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일*이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d43f0-309">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d43f0-310">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d43f0-311">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d43f0-312">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d43f0-313">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d43f0-314">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d43f0-315">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d43f0-316">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d43f0-317">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d43f0-318">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d43f0-319">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="d43f0-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d43f0-320">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d43f0-321">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d43f0-322">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d43f0-323">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d43f0-324">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="d43f0-324">Service user account</span></span>

<span data-ttu-id="d43f0-325">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d43f0-326">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-327">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d43f0-328">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d43f0-329">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d43f0-330">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d43f0-331">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d43f0-332">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d43f0-333">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="d43f0-333">Log on as a service rights</span></span>

<span data-ttu-id="d43f0-334">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="d43f0-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d43f0-335">*secpol.msc*를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d43f0-336">**로컬 정책** 노드를 확장하고 **사용자 권한 할당**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d43f0-337">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d43f0-338">**사용자 또는 그룹 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d43f0-339">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d43f0-340">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d43f0-341">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-341">Select **Advanced**.</span></span> <span data-ttu-id="d43f0-342">**지금 찾기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-342">Select **Find Now**.</span></span> <span data-ttu-id="d43f0-343">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-343">Select the user account from the list.</span></span> <span data-ttu-id="d43f0-344">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-344">Select **OK**.</span></span> <span data-ttu-id="d43f0-345">다시 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d43f0-346">**확인** 또는 **적용**을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d43f0-347">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="d43f0-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d43f0-348">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="d43f0-348">Create a service</span></span>

<span data-ttu-id="d43f0-349">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d43f0-350">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d43f0-351">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d43f0-352">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d43f0-353">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d43f0-354">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d43f0-355">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d43f0-356">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d43f0-357">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d43f0-358">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d43f0-359">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d43f0-360">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="d43f0-360">Start a service</span></span>

<span data-ttu-id="d43f0-361">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-362">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d43f0-363">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="d43f0-363">Determine a service's status</span></span>

<span data-ttu-id="d43f0-364">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-365">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d43f0-366">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="d43f0-366">Stop a service</span></span>

<span data-ttu-id="d43f0-367">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d43f0-368">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="d43f0-368">Remove a service</span></span>

<span data-ttu-id="d43f0-369">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d43f0-370">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="d43f0-370">Handle starting and stopping events</span></span>

<span data-ttu-id="d43f0-371"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="d43f0-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d43f0-372">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d43f0-373">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d43f0-374">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d43f0-375">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d43f0-376">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="d43f0-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d43f0-377">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d43f0-378">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d43f0-379">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-379">Configure endpoints</span></span>

<span data-ttu-id="d43f0-380">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d43f0-381">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d43f0-382">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d43f0-383">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d43f0-384">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d43f0-385">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d43f0-386">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d43f0-386">Current directory and content root</span></span>

<span data-ttu-id="d43f0-387">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d43f0-388">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d43f0-389">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d43f0-390">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="d43f0-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d43f0-391"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d43f0-392">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d43f0-393">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d43f0-394">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="d43f0-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d43f0-395">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d43f0-396">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d43f0-396">Troubleshoot</span></span>

<span data-ttu-id="d43f0-397">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d43f0-398">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="d43f0-398">Common errors</span></span>

* <span data-ttu-id="d43f0-399">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d43f0-400">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d43f0-401">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d43f0-402">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d43f0-403">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d43f0-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d43f0-405">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d43f0-406">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d43f0-407">Windows Service의 기본 경로는 *c:\\Windows\\System32*입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d43f0-408">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d43f0-409">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d43f0-410">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d43f0-411">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d43f0-412">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d43f0-412">System and Application Event Logs</span></span>

<span data-ttu-id="d43f0-413">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="d43f0-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d43f0-414">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d43f0-415">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d43f0-416">**시스템**을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d43f0-417">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d43f0-418">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d43f0-419">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-419">Run the app at a command prompt</span></span>

<span data-ttu-id="d43f0-420">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d43f0-421">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d43f0-422">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d43f0-423">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="d43f0-423">Clear package caches</span></span>

<span data-ttu-id="d43f0-424">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d43f0-425">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d43f0-426">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d43f0-427">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d43f0-428">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d43f0-429">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d43f0-430">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d43f0-431">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d43f0-432">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d43f0-433">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="d43f0-433">Slow or hanging app</span></span>

<span data-ttu-id="d43f0-434">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d43f0-435">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="d43f0-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="d43f0-436">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d43f0-437">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d43f0-438">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d43f0-439">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d43f0-440">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="d43f0-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d43f0-441">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d43f0-442">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d43f0-443">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d43f0-444">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d43f0-445">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d43f0-446">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="d43f0-446">Analyze the dump</span></span>

<span data-ttu-id="d43f0-447">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d43f0-448">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d43f0-449">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d43f0-449">Additional resources</span></span>

* <span data-ttu-id="d43f0-450">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="d43f0-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d43f0-451">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="d43f0-452">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="d43f0-453">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d43f0-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d43f0-454">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d43f0-454">Prerequisites</span></span>

* [<span data-ttu-id="d43f0-455">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="d43f0-456">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="d43f0-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="d43f0-457">앱 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-457">App configuration</span></span>

<span data-ttu-id="d43f0-458">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="d43f0-459">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="d43f0-460">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="d43f0-461">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="d43f0-462">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="d43f0-463"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="d43f0-464"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="d43f0-465">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="d43f0-466">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="d43f0-467"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="d43f0-468">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="d43f0-469">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="d43f0-470">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="d43f0-471">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="d43f0-472">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="d43f0-473">배포 유형</span><span class="sxs-lookup"><span data-stu-id="d43f0-473">Deployment type</span></span>

<span data-ttu-id="d43f0-474">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="d43f0-475">SDK</span><span class="sxs-lookup"><span data-stu-id="d43f0-475">SDK</span></span>

<span data-ttu-id="d43f0-476">Razor Pages 또는 MVC 프레임워크를 사용하는 웹앱 기반 서비스의 경우 프로젝트 파일에서 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="d43f0-477">서비스가 백그라운드 작업(예: [호스팅된 서비스](xref:fundamentals/host/hosted-services))만 실행하는 경우 프로젝트 파일에서 작업자 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="d43f0-478">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="d43f0-479">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="d43f0-480">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일*이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="d43f0-481">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))에는 대상 프레임워크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="d43f0-482">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="d43f0-483">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="d43f0-484">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="d43f0-485">`<UseAppHost>` 속성은 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="d43f0-486">이 속성은 서비스에 FDD의 활성화 경로(실행 파일, *.exe*)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="d43f0-487">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="d43f0-488">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="d43f0-489">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="d43f0-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="d43f0-490">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="d43f0-491">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="d43f0-492">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="d43f0-493">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="d43f0-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="d43f0-494">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="d43f0-495">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="d43f0-496">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="d43f0-497">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="d43f0-498">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="d43f0-498">Service user account</span></span>

<span data-ttu-id="d43f0-499">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="d43f0-500">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-501">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="d43f0-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="d43f0-502">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="d43f0-503">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="d43f0-504">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="d43f0-505">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="d43f0-506">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="d43f0-507">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="d43f0-507">Log on as a service rights</span></span>

<span data-ttu-id="d43f0-508">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="d43f0-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="d43f0-509">*secpol.msc*를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="d43f0-510">**로컬 정책** 노드를 확장하고 **사용자 권한 할당**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="d43f0-511">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="d43f0-512">**사용자 또는 그룹 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="d43f0-513">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="d43f0-514">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="d43f0-515">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-515">Select **Advanced**.</span></span> <span data-ttu-id="d43f0-516">**지금 찾기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-516">Select **Find Now**.</span></span> <span data-ttu-id="d43f0-517">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-517">Select the user account from the list.</span></span> <span data-ttu-id="d43f0-518">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-518">Select **OK**.</span></span> <span data-ttu-id="d43f0-519">다시 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="d43f0-520">**확인** 또는 **적용**을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="d43f0-521">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="d43f0-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="d43f0-522">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="d43f0-522">Create a service</span></span>

<span data-ttu-id="d43f0-523">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="d43f0-524">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="d43f0-525">`{EXE PATH}`: 호스트의 앱 폴더 경로입니다(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="d43f0-526">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="d43f0-527">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="d43f0-528">`{DOMAIN OR COMPUTER NAME\USER}`: 서비스 사용자 계정입니다(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="d43f0-529">`{SERVICE NAME}`: 서비스 이름입니다(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="d43f0-530">`{EXE FILE PATH}`: 앱의 실행 파일 경로입니다(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="d43f0-531">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="d43f0-532">`{DESCRIPTION}`: 서비스 설명입니다(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="d43f0-533">`{DISPLAY NAME}`: 서비스 표시 이름입니다(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="d43f0-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="d43f0-534">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="d43f0-534">Start a service</span></span>

<span data-ttu-id="d43f0-535">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-536">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="d43f0-537">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="d43f0-537">Determine a service's status</span></span>

<span data-ttu-id="d43f0-538">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="d43f0-539">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="d43f0-540">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="d43f0-540">Stop a service</span></span>

<span data-ttu-id="d43f0-541">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="d43f0-542">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="d43f0-542">Remove a service</span></span>

<span data-ttu-id="d43f0-543">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="d43f0-544">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="d43f0-544">Handle starting and stopping events</span></span>

<span data-ttu-id="d43f0-545"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="d43f0-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="d43f0-546">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="d43f0-547">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="d43f0-548">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="d43f0-549">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d43f0-550">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="d43f0-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d43f0-551">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="d43f0-552">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="d43f0-553">엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="d43f0-553">Configure endpoints</span></span>

<span data-ttu-id="d43f0-554">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="d43f0-555">`ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="d43f0-556">추가 URL 및 포트 구성 방법에 대한 자세한 내용은 관련 서버 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="d43f0-557">위의 지침에서는 HTTPS 엔드포인트에 대한 지원을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="d43f0-558">예를 들어 Windows 서비스에 인증을 사용하는 경우 HTTPS에 대해 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="d43f0-559">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="d43f0-560">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d43f0-560">Current directory and content root</span></span>

<span data-ttu-id="d43f0-561">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="d43f0-562">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="d43f0-563">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="d43f0-564">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="d43f0-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="d43f0-565"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="d43f0-566">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="d43f0-567">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="d43f0-568">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="d43f0-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="d43f0-569">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d43f0-570">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d43f0-570">Troubleshoot</span></span>

<span data-ttu-id="d43f0-571">Windows Service 앱 문제를 해결하려면 <xref:test/troubleshoot>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="d43f0-572">일반적인 오류</span><span class="sxs-lookup"><span data-stu-id="d43f0-572">Common errors</span></span>

* <span data-ttu-id="d43f0-573">이전 또는 시험판 버전의 PowerShell을 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="d43f0-574">등록된 서비스가 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에서 앱의 **게시된** 출력을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d43f0-575">[dotnet build](/dotnet/core/tools/dotnet-build) 명령의 출력은 앱 배포에 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="d43f0-576">게시된 자산은 배포 유형에 따라 다음 폴더 중 하나에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="d43f0-577">*bin/Release/{TARGET FRAMEWORK}/publish*(FDD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="d43f0-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish*(SCD)</span><span class="sxs-lookup"><span data-stu-id="d43f0-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="d43f0-579">서비스가 실행 중 상태가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="d43f0-580">앱에서 사용하는 리소스(예: 인증서)의 경로가 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="d43f0-581">Windows Service의 기본 경로는 *c:\\Windows\\System32*입니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="d43f0-582">사용자에게 *서비스로 로그온* 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="d43f0-583">`New-Service` PowerShell 명령을 실행할 때 사용자 암호가 만료되었거나 잘못 전달되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="d43f0-584">앱에 ASP.NET Core 인증이 필요하지만 보안 연결(HTTPS)에 대해 구성되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="d43f0-585">요청 URL 포트가 잘못되었거나 앱에서 올바르게 구성되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="d43f0-586">시스템 및 애플리케이션 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d43f0-586">System and Application Event Logs</span></span>

<span data-ttu-id="d43f0-587">시스템 및 애플리케이션 이벤트 로그 액세스:</span><span class="sxs-lookup"><span data-stu-id="d43f0-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="d43f0-588">시작 메뉴를 열고 *이벤트 뷰어*를 검색한 다음, **이벤트 뷰어** 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="d43f0-589">**이벤트 뷰어**에서 **Windows 로그** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="d43f0-590">**시스템**을 선택하여 시스템 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="d43f0-591">**애플리케이션**을 선택하여 애플리케이션 이벤트 로그를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="d43f0-592">실패한 앱과 연결된 오류를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="d43f0-593">명령 프롬프트에서 앱 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-593">Run the app at a command prompt</span></span>

<span data-ttu-id="d43f0-594">이벤트 로그에서 대부분의 시작 오류는 유용한 정보를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="d43f0-595">호스팅 시스템의 명령 프롬프트에서 앱을 실행하여 일부 오류의 원인을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="d43f0-596">앱에서 추가 세부 정보를 기록하려면 [로그 수준](xref:fundamentals/logging/index#log-level)을 낮추거나 [개발 환경](xref:fundamentals/environments)에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="d43f0-597">패키지 캐시 지우기</span><span class="sxs-lookup"><span data-stu-id="d43f0-597">Clear package caches</span></span>

<span data-ttu-id="d43f0-598">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="d43f0-599">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="d43f0-600">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="d43f0-601">*bin* 및 *obj* 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="d43f0-602">명령 셸에서 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="d43f0-603">[nuget.exe](https://www.nuget.org/downloads) 도구에서 `nuget locals all -clear` 명령을 실행하여 패키지 캐시를 지울 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="d43f0-604">*nuget.exe*는 Windows 데스크톱 운영 체제와 함께 제공되는 설치가 아니므로 [NuGet 웹 사이트](https://www.nuget.org/downloads)에서 별도로 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="d43f0-605">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="d43f0-606">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="d43f0-607">앱이 느리거나 중단됨</span><span class="sxs-lookup"><span data-stu-id="d43f0-607">Slow or hanging app</span></span>

<span data-ttu-id="d43f0-608">*크래시 덤프*는 시스템 메모리의 스냅샷이며 앱 충돌, 시작 실패 또는 느린 앱의 원인을 확인하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="d43f0-609">앱 충돌 또는 예외 발생</span><span class="sxs-lookup"><span data-stu-id="d43f0-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="d43f0-610">[WER(Windows 오류 보고)](/windows/desktop/wer/windows-error-reporting)에서 덤프를 얻고 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="d43f0-611">`c:\dumps`에 크래시 덤프 파일을 저장할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="d43f0-612">애플리케이션 실행 파일 이름으로 [EnableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="d43f0-613">충돌이 발생하는 조건에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="d43f0-614">충돌이 발생한 후 [DisableDumps PowerShell 스크립트](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) 실행:</span><span class="sxs-lookup"><span data-stu-id="d43f0-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="d43f0-615">앱이 충돌하고 덤프 수집이 완료되면 앱이 정상적으로 종료될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="d43f0-616">PowerShell 스크립트는 앱당 최대 5개의 덤프를 수집하도록 WER을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="d43f0-617">크래시 덤프는 많은 양의 디스크 공간(각각 여러 기가바이트까지)을 차지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="d43f0-618">앱 중단 시작 중에 실패 또는 정상적으로 실행</span><span class="sxs-lookup"><span data-stu-id="d43f0-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="d43f0-619">앱이 *중단*(응답하지 않거나 충돌하지 않음), 시작 중에 실패 또는 정상적으로 실행되면 [사용자 모드 덤프 파일: 가장 적합한 도구 선택](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)을 참조하여 덤프를 생성할 적절한 도구를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="d43f0-620">덤프 분석</span><span class="sxs-lookup"><span data-stu-id="d43f0-620">Analyze the dump</span></span>

<span data-ttu-id="d43f0-621">덤프는 여러 방법을 사용하여 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d43f0-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="d43f0-622">자세한 내용은 [사용자 모드 덤프 파일 분석](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d43f0-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d43f0-623">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d43f0-623">Additional resources</span></span>

* <span data-ttu-id="d43f0-624">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="d43f0-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
